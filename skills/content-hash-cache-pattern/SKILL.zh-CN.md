---
name: content-hash-cache-pattern
description: Cache expensive file processing results using SHA-256 content hashes — path-independent, auto-invalidating, with service layer separation.
description zh-CN: 使用 SHA-256 内容哈希缓存昂贵的文件处理结果——路径无关、自动失效、服务层分离。
origin: ECC
---

# Content-Hash File Cache Pattern
# 内容哈希文件缓存模式

Cache expensive file processing results (PDF parsing, text extraction, image analysis) using SHA-256 content hashes as cache keys. Unlike path-based caching, this approach survives file moves/renames and auto-invalidates when content changes.
使用 SHA-256 内容哈希作为缓存键，缓存昂贵的文件处理结果（PDF 解析、文本提取、图像分析）。与基于路径的缓存不同，此方法在文件移动/重命名后仍然有效，内容变化时自动失效。

## When to Activate
## 何时激活

- Building file processing pipelines (PDF, images, text extraction)
  - 构建文件处理管道（PDF、图像、文本提取）
- Processing cost is high and same files are processed repeatedly
  - 处理成本高且相同文件被重复处理
- Need a `--cache/--no-cache` CLI option
  - 需要 `--cache/--no-cache` CLI 选项
- Want to add caching to existing pure functions without modifying them
  - 想在不修改现有纯函数的情况下添加缓存

## Core Pattern
## 核心模式

### 1. Content-Hash Based Cache Key
### 1. 基于内容哈希的缓存键

Use file content (not path) as the cache key:
使用文件内容（而非路径）作为缓存键：

```python
import hashlib
from pathlib import Path

_HASH_CHUNK_SIZE = 65536  # 64KB chunks for large files

def compute_file_hash(path: Path) -> str:
    """SHA-256 of file contents (chunked for large files)."""
    if not path.is_file():
        raise FileNotFoundError(f"File not found: {path}")
    sha256 = hashlib.sha256()
    with open(path, "rb") as f:
        while True:
            chunk = f.read(_HASH_CHUNK_SIZE)
            if not chunk:
                break
            sha256.update(chunk)
    return sha256.hexdigest()
```

**Why content hash?** File rename/move = cache hit. Content change = automatic invalidation. No index file needed.
**为什么用内容哈希？** 文件重命名/移动 = 缓存命中。内容变化 = 自动失效。无需索引文件。

### 2. Frozen Dataclass for Cache Entry
### 2. 用于缓存条目的 Frozen Dataclass

```python
from dataclasses import dataclass

@dataclass(frozen=True, slots=True)
class CacheEntry:
    file_hash: str
    source_path: str
    document: ExtractedDocument  # The cached result
    document: ExtractedDocument  # 缓存的结果
```

### 3. File-Based Cache Storage
### 3. 基于文件的缓存存储

Each cache entry is stored as `{hash}.json` — O(1) lookup by hash, no index file required.
每个缓存条目存储为 `{hash}.json` —— 按哈希 O(1) 查找，无需索引文件。

```python
import json
from typing import Any

def write_cache(cache_dir: Path, entry: CacheEntry) -> None:
    cache_dir.mkdir(parents=True, exist_ok=True)
    cache_file = cache_dir / f"{entry.file_hash}.json"
    data = serialize_entry(entry)
    cache_file.write_text(json.dumps(data, ensure_ascii=False), encoding="utf-8")

def read_cache(cache_dir: Path, file_hash: str) -> CacheEntry | None:
    cache_file = cache_dir / f"{file_hash}.json"
    if not cache_file.is_file():
        return None
    try:
        raw = cache_file.read_text(encoding="utf-8")
        data = json.loads(raw)
        return deserialize_entry(data)
    except (json.JSONDecodeError, ValueError, KeyError):
        return None  # Treat corruption as cache miss
    except (json.JSONDecodeError, ValueError, KeyError):
        return None  # 将损坏视为缓存未命中
```

### 4. Service Layer Wrapper (SRP)
### 4. 服务层包装器（SRP）

Keep the processing function pure. Add caching as a separate service layer.
保持处理函数纯净。将缓存作为单独的服务层。

```python
def extract_with_cache(
    file_path: Path,
    *,
    cache_enabled: bool = True,
    cache_dir: Path = Path(".cache"),
) -> ExtractedDocument:
    """Service layer: cache check -> extraction -> cache write."""
    """服务层：缓存检查 -> 提取 -> 缓存写入。"""
    if not cache_enabled:
        return extract_text(file_path)  # Pure function, no cache knowledge
        return extract_text(file_path)  # 纯函数，不知道缓存

    file_hash = compute_file_hash(file_path)

    # Check cache
    # 检查缓存
    cached = read_cache(cache_dir, file_hash)
    if cached is not None:
        logger.info("Cache hit: %s (hash=%s)", file_path.name, file_hash[:12])
        return cached.document

    # Cache miss -> extract -> store
    # 缓存未命中 -> 提取 -> 存储
    logger.info("Cache miss: %s (hash=%s)", file_path.name, file_hash[:12])
    doc = extract_text(file_path)
    entry = CacheEntry(file_hash=file_hash, source_path=str(file_path), document=doc)
    write_cache(cache_dir, entry)
    return doc
```

## Key Design Decisions
## 关键设计决策

| Decision | Rationale |
| 决策 | 理由 |
|----------|-----------|
| SHA-256 content hash | Path-independent, auto-invalidates on content change |
| SHA-256 内容哈希 | 路径无关，内容变化时自动失效 |
| `{hash}.json` file naming | O(1) lookup, no index file needed |
| `{hash}.json` 文件命名 | O(1) 查找，无需索引文件 |
| Service layer wrapper | SRP: extraction stays pure, cache is a separate concern |
| 服务层包装器 | SRP：提取保持纯净，缓存是单独关注点 |
| Manual JSON serialization | Full control over frozen dataclass serialization |
| 手动 JSON 序列化 | 完全控制 frozen dataclass 序列化 |
| Corruption returns `None` | Graceful degradation, re-processes on next run |
| 损坏返回 `None` | 优雅降级，下次运行时重新处理 |
| `cache_dir.mkdir(parents=True)` | Lazy directory creation on first write |
| `cache_dir.mkdir(parents=True)` | 首次写入时惰性创建目录 |

## Best Practices
## 最佳实践

- **Hash content, not paths** — paths change, content identity doesn't
  - **哈希内容而非路径** —— 路径会变，内容身份不变
- **Chunk large files** when hashing — avoid loading entire files into memory
  - **对大文件分块**哈希 —— 避免将整个文件加载到内存
- **Keep processing functions pure** — they should know nothing about caching
  - **保持处理函数纯净** —— 它们应该不知道缓存的存在
- **Log cache hit/miss** with truncated hashes for debugging
  - **记录缓存命中/未命中** 用截断哈希以便调试
- **Handle corruption gracefully** — treat invalid cache entries as misses, never crash
  - **优雅处理损坏** —— 将无效缓存条目视为未命中，永不崩溃

## Anti-Patterns to Avoid
## 应避免的反模式

```python
# BAD: Path-based caching (breaks on file move/rename)
# 差：基于路径的缓存（文件移动/重命名时失效）
cache = {"/path/to/file.pdf": result}

# BAD: Adding cache logic inside the processing function (SRP violation)
# 差：在处理函数内部添加缓存逻辑（违反 SRP）
def extract_text(path, *, cache_enabled=False, cache_dir=None):
    if cache_enabled:  # Now this function has two responsibilities
        if cache_enabled:  # 现在此函数有两个职责
            ...

# BAD: Using dataclasses.asdict() with nested frozen dataclasses
# 差：对嵌套 frozen dataclasses 使用 dataclasses.asdict()
# (can cause issues with complex nested types)
# （可能导致复杂嵌套类型问题）
data = dataclasses.asdict(entry)  # Use manual serialization instead
data = dataclasses.asdict(entry)  # 改用手动序列化
```

## When to Use
## 何时使用

- File processing pipelines (PDF parsing, OCR, text extraction, image analysis)
  - 文件处理管道（PDF 解析、OCR、文本提取、图像分析）
- CLI tools that benefit from `--cache/--no-cache` options
  - 从 `--cache/--no-cache` 选项受益的 CLI 工具
- Batch processing where the same files appear across runs
  - 跨运行存在相同文件的批处理
- Adding caching to existing pure functions without modifying them
  - 在不修改现有纯函数的情况下添加缓存

## When NOT to Use
## 何时不使用

- Data that must always be fresh (real-time feeds)
  - 必须始终保持新鲜的数据（实时数据流）
- Cache entries that would be extremely large (consider streaming instead)
  - 缓存条目会非常大的情况（考虑改用流式处理）
- Results that depend on parameters beyond file content (e.g., different extraction configs)
  - 依赖文件内容以外参数的结果（例如，不同的提取配置）
