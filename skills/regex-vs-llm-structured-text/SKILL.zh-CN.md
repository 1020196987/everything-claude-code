---
name: regex-vs-llm-structured-text
description: Decision framework for choosing between regex and LLM when parsing structured text — start with regex, add LLM only for low-confidence edge cases.
description zh-CN: 解析结构化文本时选择正则表达式还是 LLM 的决策框架 — 从正则表达式开始，仅在低置信度边缘情况下添加 LLM。
origin: ECC
---

# Regex vs LLM for Structured Text Parsing
# 正则表达式与 LLM 结构化文本解析

A practical decision framework for parsing structured text (quizzes, forms, invoices, documents). The key insight: regex handles 95-98% of cases cheaply and deterministically. Reserve expensive LLM calls for the remaining edge cases.
一个实用的决策框架，用于解析结构化文本（测验、表单、发票、文档）。关键洞察：正则表达式以低成本和确定性方式处理 95-98% 的情况。将昂贵的 LLM 调用保留用于剩余的边缘情况。

## When to Activate
## 何时激活

- Parsing structured text with repeating patterns (questions, forms, tables)
- 解析具有重复模式的结构化文本（问题、表单、表格）
- Deciding between regex and LLM for text extraction
- 决定使用正则表达式还是 LLM 进行文本提取
- Building hybrid pipelines that combine both approaches
- 构建结合两种方法的混合管道
- Optimizing cost/accuracy tradeoffs in text processing
- 优化文本处理的成本/准确度权衡

## Decision Framework
## 决策框架

```
Is the text format consistent and repeating?
├── Yes (>90% follows a pattern) → Start with Regex
│   ├── Regex handles 95%+ → Done, no LLM needed
│   └── Regex handles <95% → Add LLM for edge cases only
└── No (free-form, highly variable) → Use LLM directly
```

文本格式是否一致且重复？
├── 是（>90% 遵循模式）→ 从正则表达式开始
│   ├── 正则表达式处理 95%+ → 完成，无需 LLM
│   └── 正则表达式处理 <95% → 仅对边缘情况添加 LLM
└── 否（自由格式、高度可变）→ 直接使用 LLM

## Architecture Pattern
## 架构模式

```
Source Text
    │
    ▼
[Regex Parser] ─── Extracts structure (95-98% accuracy)
    │
    ▼
[Text Cleaner] ─── Removes noise (markers, page numbers, artifacts)
    │
    ▼
[Confidence Scorer] ─── Flags low-confidence extractions
    │
    ├── High confidence (≥0.95) → Direct output
    │
    └── Low confidence (<0.95) → [LLM Validator] → Output
```

源文本
    │
    ▼
[正则解析器] ─── 提取结构（95-98% 准确率）
    │
    ▼
[文本清理器] ─── 移除噪音（标记、页码、伪影）
    │
    ▼
[置信度评分器] ─── 标记低置信度提取
    │
    ├── 高置信度 (≥0.95) → 直接输出
    │
    └── 低置信度 (<0.95) → [LLM 验证器] → 输出

## Implementation
## 实现

### 1. Regex Parser (Handles the Majority)
### 1. 正则解析器（处理大多数情况）

```python
import re
from dataclasses import dataclass

@dataclass(frozen=True)
class ParsedItem:
    id: str
    text: str
    choices: tuple[str, ...]
    answer: str
    confidence: float = 1.0

def parse_structured_text(content: str) -> list[ParsedItem]:
    """Parse structured text using regex patterns."""
    pattern = re.compile(
        r"(?P<id>\d+)\.\s*(?P<text>.+?)\n"
        r"(?P<choices>(?:[A-D]\..+?\n)+)"
        r"Answer:\s*(?P<answer>[A-D])",
        re.MULTILINE | re.DOTALL,
    )
    items = []
    for match in pattern.finditer(content):
        choices = tuple(
            c.strip() for c in re.findall(r"[A-D]\.\s*(.+)", match.group("choices"))
        )
        items.append(ParsedItem(
            id=match.group("id"),
            text=match.group("text").strip(),
            choices=choices,
            answer=match.group("answer"),
        ))
    return items
```

### 2. Confidence Scoring
### 2. 置信度评分

Flag items that may need LLM review:
标记可能需要 LLM 审查的项目：

```python
@dataclass(frozen=True)
class ConfidenceFlag:
    item_id: str
    score: float
    reasons: tuple[str, ...]

def score_confidence(item: ParsedItem) -> ConfidenceFlag:
    """Score extraction confidence and flag issues."""
    reasons = []
    score = 1.0

    if len(item.choices) < 3:
        reasons.append("few_choices")
        score -= 0.3

    if not item.answer:
        reasons.append("missing_answer")
        score -= 0.5

    if len(item.text) < 10:
        reasons.append("short_text")
        score -= 0.2

    return ConfidenceFlag(
        item_id=item.id,
        score=max(0.0, score),
        reasons=tuple(reasons),
    )

def identify_low_confidence(
    items: list[ParsedItem],
    threshold: float = 0.95,
) -> list[ConfidenceFlag]:
    """Return items below confidence threshold."""
    flags = [score_confidence(item) for item in items]
    return [f for f in flags if f.score < threshold]
```

### 3. LLM Validator (Edge Cases Only)
### 3. LLM 验证器（仅边缘情况）

```python
def validate_with_llm(
    item: ParsedItem,
    original_text: str,
    client,
) -> ParsedItem:
    """Use LLM to fix low-confidence extractions."""
    response = client.messages.create(
        model="claude-haiku-4-5-20251001",  # Cheapest model for validation
        max_tokens=500,
        messages=[{
            "role": "user",
            "content": (
                f"Extract the question, choices, and answer from this text.\n\n"
                f"Text: {original_text}\n\n"
                f"Current extraction: {item}\n\n"
                f"Return corrected JSON if needed, or 'CORRECT' if accurate."
            ),
        }],
    )
    # Parse LLM response and return corrected item...
    return corrected_item
```

### 4. Hybrid Pipeline
### 4. 混合管道

```python
def process_document(
    content: str,
    *,
    llm_client=None,
    confidence_threshold: float = 0.95,
) -> list[ParsedItem]:
    """Full pipeline: regex -> confidence check -> LLM for edge cases."""
    # Step 1: Regex extraction (handles 95-98%)
    items = parse_structured_text(content)

    # Step 2: Confidence scoring
    low_confidence = identify_low_confidence(items, confidence_threshold)

    if not low_confidence or llm_client is None:
        return items

    # Step 3: LLM validation (only for flagged items)
    low_conf_ids = {f.item_id for f in low_confidence}
    result = []
    for item in items:
        if item.id in low_conf_ids:
            result.append(validate_with_llm(item, content, llm_client))
        else:
            result.append(item)

    return result
```

## Real-World Metrics
## 实际指标

From a production quiz parsing pipeline (410 items):
来自生产环境测验解析管道（410 个项目）：

| Metric | Value |
|--------|-------|
| Regex success rate | 98.0% |
| Low confidence items | 8 (2.0%) |
| LLM calls needed | ~5 |
| Cost savings vs all-LLM | ~95% |
| Test coverage | 93% |

| 指标 | 值 |
|--------|-------|
| 正则表达式成功率 | 98.0% |
| 低置信度项目 | 8 (2.0%) |
| 需要的 LLM 调用 | ~5 |
| 相比全 LLM 的成本节省 | ~95% |
| 测试覆盖率 | 93% |

## Best Practices
## 最佳实践

- **Start with regex** — even imperfect regex gives you a baseline to improve
- **从正则表达式开始** — 即使不完美的正则表达式也能提供改进的基线
- **Use confidence scoring** to programmatically identify what needs LLM help
- **使用置信度评分** 以编程方式识别需要 LLM 帮助的内容
- **Use the cheapest LLM** for validation (Haiku-class models are sufficient)
- **使用最便宜的 LLM** 进行验证（Haiku 级模型已足够）
- **Never mutate** parsed items — return new instances from cleaning/validation steps
- **永远不要变更** 解析后的项目 — 从清理/验证步骤返回新实例
- **TDD works well** for parsers — write tests for known patterns first, then edge cases
- **TDD 适合解析器** — 先为已知模式编写测试，再处理边缘情况
- **Log metrics** (regex success rate, LLM call count) to track pipeline health
- **记录指标**（正则表达式成功率、LLM 调用次数）以跟踪管道健康状况

## Anti-Patterns to Avoid
## 应避免的反模式

- Sending all text to an LLM when regex handles 95%+ of cases (expensive and slow)
- 当正则表达式处理 95%+ 时将所有文本发送给 LLM（昂贵且缓慢）
- Using regex for free-form, highly variable text (LLM is better here)
- 对自由格式、高度可变的文本使用正则表达式（LLM 在这里更好）
- Skipping confidence scoring and hoping regex "just works"
- 跳过置信度评分并希望正则表达式"正常工作"
- Mutating parsed objects during cleaning/validation steps
- 在清理/验证步骤中变更解析对象
- Not testing edge cases (malformed input, missing fields, encoding issues)
- 不测试边缘情况（格式错误的输入、缺失字段、编码问题）

## When to Use
## 何时使用

- Quiz/exam question parsing
- 测验/考试问题解析
- Form data extraction
- 表单数据提取
- Invoice/receipt processing
- 发票/收据处理
- Document structure parsing (headers, sections, tables)
- 文档结构解析（标题、章节、表格）
- Any structured text with repeating patterns where cost matters
- 任何成本重要的具有重复模式的结构化文本
