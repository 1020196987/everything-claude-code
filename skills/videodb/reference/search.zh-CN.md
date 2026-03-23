# Search & Indexing Guide

## Search & Indexing Guide
## 搜索和索引指南

Search allows you to find specific moments inside videos using natural language queries, exact keywords, or visual scene descriptions.
搜索允许您使用自然语言查询、精确关键词或视觉场景描述在视频中找到特定时刻。

## Prerequisites

## Prerequisites
## 前提条件

Videos **must be indexed** before they can be searched. Indexing is a one-time operation per video per index type.
视频**必须被索引**后才能被搜索。每个视频每种索引类型只需索引一次。

## Indexing

## Indexing
## 索引

### Spoken Word Index

### Spoken Word Index
### 口语索引

Index the transcribed speech content of a video for semantic and keyword search:
为视频的转录语音内容建立索引以进行语义和关键词搜索：

```python
video = coll.get_video(video_id)

# force=True makes indexing idempotent — skips if already indexed
video.index_spoken_words(force=True)
```

This transcribes the audio track and builds a searchable index over the spoken content. Required for semantic search and keyword search.
这会转录音轨并对口语内容构建可搜索索引。语义搜索和关键词搜索需要此操作。

**Parameters:**
**参数：**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `language_code` | `str\|None` | `None` | Language code of the video |
| `language_code` | `str\|None` | `None` | 视频的语言代码 |
| `segmentation_type` | `SegmentationType` | `SegmentationType.sentence` | Segmentation type (`sentence` or `llm`) |
| `segmentation_type` | `SegmentationType` | `SegmentationType.sentence` | 分段类型（`sentence` 或 `llm`） |
| `force` | `bool` | `False` | Set to `True` to skip if already indexed (avoids "already exists" error) |
| `force` | `bool` | `False` | 设置为 `True` 以在已索引时跳过（避免"已存在"错误） |
| `callback_url` | `str\|None` | `None` | Webhook URL for async notification |
| `callback_url` | `str\|None` | `None` | 异步通知的 Webhook URL |

### Scene Index

### Scene Index
### 场景索引

Index visual content by generating AI descriptions of scenes. Like spoken word indexing, this raises an error if a scene index already exists. Extract the existing `scene_index_id` from the error message.
通过生成场景的 AI 描述来索引视觉内容。与口语索引一样，如果场景索引已存在会引发错误。从错误消息中提取现有的 `scene_index_id`。

```python
import re
from videodb import SceneExtractionType

try:
    scene_index_id = video.index_scenes(
        extraction_type=SceneExtractionType.shot_based,
        prompt="Describe the visual content, objects, actions, and setting in this scene.",
    )
except Exception as e:
    match = re.search(r"id\s+([a-f0-9]+)", str(e))
    if match:
        scene_index_id = match.group(1)
    else:
        raise
```

**Extraction types:**
**提取类型：**

| Type | Description | Best For |
|------|-------------|----------|
| `SceneExtractionType.shot_based` | Splits on visual shot boundaries | General purpose, action content |
| `SceneExtractionType.shot_based` | 在视觉镜头边界处分割 | 通用、动作内容 |
| `SceneExtractionType.time_based` | Splits at fixed intervals | Uniform sampling, long static content |
| `SceneExtractionType.time_based` | 在固定间隔分割 | 均匀采样、长时间静态内容 |
| `SceneExtractionType.transcript` | Splits based on transcript segments | Speech-driven scene boundaries |
| `SceneExtractionType.transcript` | 基于转录片段分割 | 语音驱动的场景边界 |

**Parameters for `time_based`:**
**`time_based` 的参数：**

```python
video.index_scenes(
    extraction_type=SceneExtractionType.time_based,
    extraction_config={"time": 5, "select_frames": ["first", "last"]},
    prompt="Describe what is happening in this scene.",
)
```

## Search Types

## Search Types
## 搜索类型

### Semantic Search

### Semantic Search
### 语义搜索

Natural language queries matched against spoken content:
针对口语内容的自然语言查询匹配：

```python
from videodb import SearchType

results = video.search(
    query="explaining the benefits of machine learning",
    search_type=SearchType.semantic,
)
```

Returns ranked segments where the spoken content semantically matches the query.
返回口语内容在语义上与查询匹配的排名片段。

### Keyword Search

### Keyword Search
### 关键词搜索

Exact term matching in transcribed speech:
转录语音中的精确术语匹配：

```python
results = video.search(
    query="artificial intelligence",
    search_type=SearchType.keyword,
)
```

Returns segments containing the exact keyword or phrase.
返回包含精确关键词或短语的片段。

### Scene Search

### Scene Search
### 场景搜索

Visual content queries matched against indexed scene descriptions. Requires a prior `index_scenes()` call.
针对索引场景描述的视觉内容查询。需要先调用 `index_scenes()`。

`index_scenes()` returns a `scene_index_id`. Pass it to `video.search()` to target a specific scene index (especially important when a video has multiple scene indexes):
`index_scenes()` 返回一个 `scene_index_id`。将其传递给 `video.search()` 以针对特定场景索引（当视频有多个场景索引时尤为重要）：

```python
from videodb import SearchType, IndexType
from videodb.exceptions import InvalidRequestError

# Search using semantic search against the scene index.
# Use score_threshold to filter low-relevance noise (recommended: 0.3+).
try:
    results = video.search(
        query="person writing on a whiteboard",
        search_type=SearchType.semantic,
        index_type=IndexType.scene,
        scene_index_id=scene_index_id,
        score_threshold=0.3,
    )
    shots = results.get_shots()
except InvalidRequestError as e:
    if "No results found" in str(e):
        shots = []
    else:
        raise
```

**Important notes:**
**重要说明：**

- Use `SearchType.semantic` with `index_type=IndexType.scene` — this is the most reliable combination and works on all plans.
  - 使用 `SearchType.semantic` 配合 `index_type=IndexType.scene`——这是最可靠的组合，适用于所有计划。
- `SearchType.scene` exists but may not be available on all plans (e.g. Free tier). Prefer `SearchType.semantic` with `IndexType.scene`.
  - `SearchType.scene` 存在，但可能并非在所有计划上都可用（例如免费层）。优先使用 `SearchType.semantic` 配合 `IndexType.scene`。
- The `scene_index_id` parameter is optional. If omitted, the search runs against all scene indexes on the video. Pass it to target a specific index.
  - `scene_index_id` 参数是可选的。如果省略，搜索将针对视频上的所有场景索引。传递它以针对特定索引。
- You can create multiple scene indexes per video (with different prompts or extraction types) and search them independently using `scene_index_id`.
  - 您可以为每个视频创建多个场景索引（使用不同的提示或提取类型），并使用 `scene_index_id` 独立搜索它们。

### Scene Search with Metadata Filtering

### Scene Search with Metadata Filtering
### 带元数据过滤的场景搜索

When indexing scenes with custom metadata, you can combine semantic search with metadata filters:
当使用自定义元数据索引场景时，您可以结合语义搜索和元数据过滤器：

```python
from videodb import SearchType, IndexType

results = video.search(
    query="a skillful chasing scene",
    search_type=SearchType.semantic,
    index_type=IndexType.scene,
    scene_index_id=scene_index_id,
    filter=[{"camera_view": "road_ahead"}, {"action_type": "chasing"}],
)
```

See the [scene_level_metadata_indexing cookbook](https://github.com/video-db/videodb-cookbook/blob/main/quickstart/scene_level_metadata_indexing.ipynb) for a full example of custom metadata indexing and filtered search.
有关自定义元数据索引和过滤搜索的完整示例，参见 [scene_level_metadata_indexing cookbook](https://github.com/video-db/videodb-cookbook/blob/main/quickstart/scene_level_metadata_indexing.ipynb)。

## Working with Results

## Working with Results
## 处理结果

### Get Shots

### Get Shots
### 获取片段

Access individual result segments:
访问单个结果片段：

```python
results = video.search("your query")

for shot in results.get_shots():
    print(f"Video: {shot.video_id}")
    print(f"Start: {shot.start:.2f}s")
    print(f"End: {shot.end:.2f}s")
    print(f"Text: {shot.text}")
    print("---")
```

### Play Compiled Results

### Play Compiled Results
### 播放编译结果

Stream all matching segments as a single compiled video:
将所有匹配的片段作为单个编译视频流式传输：

```python
results = video.search("your query")
stream_url = results.compile()
results.play()  # opens compiled stream in browser
```

### Extract Clips

### Extract Clips
### 提取片段

Download or stream specific result segments:
下载或流式传输特定结果片段：

```python
for shot in results.get_shots():
    stream_url = shot.generate_stream()
    print(f"Clip: {stream_url}")
```

## Cross-Collection Search

## Cross-Collection Search
## 跨 Collection 搜索

Search across all videos in a collection:
在 collection 中的所有视频中搜索：

```python
coll = conn.get_collection()

# Search across all videos in the collection
results = coll.search(
    query="product demo",
    search_type=SearchType.semantic,
)

for shot in results.get_shots():
    print(f"Video: {shot.video_id} [{shot.start:.1f}s - {shot.end:.1f}s]")
```

> **Note:** Collection-level search only supports `SearchType.semantic`. Using `SearchType.keyword` or `SearchType.scene` with `coll.search()` will raise `NotImplementedError`. For keyword or scene search, use `video.search()` on individual videos instead.
> **注意：** Collection 级别的搜索仅支持 `SearchType.semantic`。将 `SearchType.keyword` 或 `SearchType.scene` 与 `coll.search()` 一起使用会引发 `NotImplementedError`。对于关键词或场景搜索，改为在单个视频上使用 `video.search()`。

## Search + Compile

## Search + Compile
## 搜索 + 编译

Index, search, and compile matching segments into a single playable stream:
索引、搜索并将匹配的片段编译成单个可播放流：

```python
video.index_spoken_words(force=True)
results = video.search(query="your query", search_type=SearchType.semantic)
stream_url = results.compile()
print(stream_url)
```

## Tips

## Tips
## 提示

- **Index once, search many times**: Indexing is the expensive operation. Once indexed, searches are fast.
  - **一次索引，多次搜索**：索引是昂贵的操作。一旦索引，搜索就很快。
- **Combine index types**: Index both spoken words and scenes to enable all search types on the same video.
  - **组合索引类型**：同时索引口语和场景，以在同一视频上启用所有搜索类型。
- **Refine queries**: Semantic search works best with descriptive, natural language phrases rather than single keywords.
  - **优化查询**：语义搜索最适合描述性的自然语言短语，而不是单个关键词。
- **Use keyword search for precision**: When you need exact term matches, keyword search avoids semantic drift.
  - **使用关键词搜索以提高精确度**：当您需要精确术语匹配时，关键词搜索避免语义漂移。
- **Handle "No results found"**: `video.search()` raises `InvalidRequestError` when no results match. Always wrap search calls in try/except and treat `"No results found"` as an empty result set.
  - **处理"No results found"**：`video.search()` 在没有匹配结果时引发 `InvalidRequestError`。始终将搜索调用包装在 try/except 中，并将 `"No results found"` 视为空结果集。
- **Filter scene search noise**: Semantic scene search can return low-relevance results for vague queries. Use `score_threshold=0.3` (or higher) to filter noise.
  - **过滤场景搜索噪音**：语义场景搜索可能对模糊查询返回低相关性结果。使用 `score_threshold=0.3`（或更高）来过滤噪音。
- **Idempotent indexing**: Use `index_spoken_words(force=True)` to safely re-index. `index_scenes()` has no `force` parameter — wrap it in try/except and extract the existing `scene_index_id` from the error message with `re.search(r"id\s+([a-f0-9]+)", str(e))`.
  - **幂等索引**：使用 `index_spoken_words(force=True)` 安全地重新索引。`index_scenes()` 没有 `force` 参数——将其包装在 try/except 中，并使用 `re.search(r"id\s+([a-f0-9]+)", str(e))` 从错误消息中提取现有的 `scene_index_id`。
