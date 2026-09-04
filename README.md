# K12 Knowledge Points 知识点数据集

中国 K12 全学段（小学/初中/高中）知识点及其关系图谱数据集，从真实教材中抽取构建。

## 数据规模

| 指标 | 数值 |
|---|---|
| 知识点总数 | 33,765 |
| 小学 | 12,373 |
| 初中 | 12,731 |
| 高中 | 8,661 |
| 关系总数 | 21,536（prerequisite / leads-to / related）|
| 覆盖教材 | 347 本（其中 293 本有教材正文支撑抽取，54 本正文缺失、仅按目录标题抽取，置信度较低）|
| 覆盖科目 | 17 个 |

## 文件结构

```
knowledge_points.json   # 全量知识点（70MB）
kp_relations.json       # 全量关系（9.6MB）
meta.json               # 元信息
split/                  # 按学段×科目拆分（推荐使用，单文件 200KB–11MB）
  index.json            # 拆分索引：文件清单 + 各组计数
  小学/<科目>.json
  初中/<科目>.json
  高中/<科目>.json
```

`split/` 下每个文件包含该学段×科目的 `knowledge_points` 与 `relations` 两个数组。所有关系均不跨科目、不跨学段，可独立按组使用。

## 字段说明

**knowledge_points**

| 字段 | 说明 |
|---|---|
| `id` | 知识点唯一 ID |
| `canonical_name` | 规范名称 |
| `subject` | 科目 |
| `grade_band` | 学段（小学/初中/高中）|
| `curriculum_system` | 课程体系 |
| `confidence` | 抽取置信度（0–1）|
| `aliases` | 别名列表（JSON 字符串）|
| `annotations` | 标注信息（JSON 字符串）|
| `summary` | 摘要 |
| `title` | 标题 |

**kp_relations / relations**

| 字段 | 说明 |
|---|---|
| `id` | 关系唯一 ID |
| `from_kp_id` / `to_kp_id` | 起止知识点 ID |
| `relation_type` | `prerequisite`（前置）/ `leads-to`（导向）/ `related`（相关）|
| `source_evidence` | 来源证据 |
| `from_name` / `to_name` | 起止知识点名称（冗余字段，便于阅读）|

## 构建方式

教材页面 OCR → LLM 知识点抽取（qwen-plus）→ 低置信度清理 → 同科目去重合并 → LLM 关系推断。
