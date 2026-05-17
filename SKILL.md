# AITOP Skill

让 Agent 直接读取 AITOP 的全部 AI 动态和每日精编日报。匿名免费，无需 API Key，无需配 MCP server。

## 前置要求

**User-Agent（必须）**：所有 `/api/public/*` 请求必须带真实 UA，推荐附加标识符：

```
Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36 aitop-skill/1.0
```

默认 `curl/x.y.z` 返回 403。浏览器 fetch / 主流 SDK 默认带 UA，无需处理。

## 端点一览

| 端点 | 用途 |
|------|------|
| `GET /api/public/items` | AI 动态列表，cursor 翻页，支持过滤 |
| `GET /api/public/daily` | 最新一期日报 |
| `GET /api/public/daily/{YYYY-MM-DD}` | 指定日期日报 |
| `GET /api/public/dailies` | 有日报的日期列表（discovery） |

**Base URL**: `https://aitop.news`

## 路由决策

**默认（宽泛问题）**：用 `mode=selected` + 时间窗。适用于"今天 AI 圈有啥"、"最近发生什么"等开放性问题。

**日报**：仅当用户话里出现**"日报"**二字时才走 `/daily`。"今天 AI 圈"、"过去 24 小时大新闻"等宽问题是滚动时间窗查询，不等于日报（日报是 UTC 0 点切片的固定成品，时间精度不同）。

**全量**：仅当用户明确说**"全部 / 完整 / 所有 / 全量"**时才用 `mode=all`。默认 `mode=selected`，精选已覆盖大多数用户关心的内容。

### 意图 → 端点映射

| 用户意图 | 调用端点 |
|---------|---------|
| 默认（宽泛问题） | `GET /items?mode=selected&since=<时间窗>&take=50` |
| 明确说"日报" | `GET /daily` 或 `GET /daily/{YYYY-MM-DD}` |
| 明确说"全部 / 完整 / 全量" | `GET /items?mode=all&take=50` |
| 带分类："AI 模型 / 产品 / 论文 / 技巧" | `GET /items?mode=selected&category=<slug>&take=50` |
| 带时间窗："最近 3 天" | `GET /items?mode=selected&since=72h&take=50` |
| 关键词搜索："OpenAI 最近发的" | `GET /items?q=OpenAI&take=20` |
| 日期 discovery："哪些日期有日报" | `GET /dailies?take=30` |

### 时间窗推断

| 用户说 | since 值 |
|--------|---------|
| "今天" / "今日" | `since=24h` |
| "昨天" | `since=48h`（保守覆盖） |
| "最近 N 天" | `since=<N×24>h` |
| "这周" / "本周" | `since=168h` |
| 未指定时间 | `since=24h`（默认） |

### category 枚举（仅 5 个有效值）

| slug | 含义 |
|------|------|
| `ai-models` | AI 模型发布 / 更新 |
| `ai-products` | AI 产品 / 工具 |
| `industry` | 行业动态 / 融资 / 政策 |
| `paper` | 研究论文 |
| `tip` | 使用技巧 / 教程 |

## 响应 Schema

### GET /items

```json
{
  "hasMore": true,
  "nextCursor": "eyJhIjoxNzc4NjU4Njk2MDAwLCJpIjoiMjQxNyJ9",
  "items": [
    {
      "id": "2436",
      "sourceName": "IT之家",
      "url": "https://www.ithome.com/0/949/956.htm",
      "title": "小米开源 OneVL 自动驾驶模型，统一 VLA 与世界模型",
      "title_en": null,
      "summary": "小米技术发布并开源了 Xiaomi OneVL 一步式潜空间语言视觉推理框架...",
      "category": "ai-models",
      "channel": "blog",
      "isFeatured": true,
      "tags": ["自动驾驶", "VLA", "世界模型", "开源/仓库", "小米"],
      "publishedAt": "2026-05-13T09:17:07Z",
      "media": [
        { "kind": "image", "url": "/api/img-proxy?u=https%3A%2F%2F..." }
      ]
    }
  ]
}
```

**字段约束**：

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | `string` | 数字字符串，不要假设格式或长度 |
| `sourceName` | `string` | 必有 |
| `url` | `string` | 必有，原文链接 |
| `title` | `string` | 必有 |
| `title_en` | `string \| null` | 英文标题；当前版本始终为 `null` |
| `summary` | `string \| null` | AI 生成摘要；未处理时为 `null` |
| `category` | `string \| null` | 仅上表 5 个 slug + `null`，不会出现其他值 |
| `channel` | `string` | `firstParty` · `news` · `x` · `community` · `blog` · `paper` |
| `isFeatured` | `boolean` | 是否精选 |
| `tags` | `string[]` | 可为空数组 |
| `publishedAt` | `string \| null` | ISO 8601 UTC（带 Z）；来源无明确发布时间时为 `null` |
| `media[].url` | `string` | 相对路径，完整 URL = `https://aitop.news` + `media[].url` |

### cursor 翻页

```
# 首次请求（不带 cursor）
GET /items?mode=selected&since=24h&take=50

# 用上一响应的 nextCursor 继续
GET /items?mode=selected&take=50&cursor=<nextCursor>

# hasMore=false 或 nextCursor=null 时停止
# cursor 损坏 / 过期时自动从首页开始，不报错
```

大多数场景不需要翻页：`since=24h&take=50` 一次能覆盖当天全部精选。

### GET /daily 和 /daily/{date}

```json
{
  "id": "51",
  "date": "2026-05-13",
  "headline": "Anthropic与谷歌齐发力，AI智能体时代加速到来",
  "summaryMd": "2026年5月13日，AI领域在**智能体**、**开源生态**...",
  "stats": { "stories": 213, "firstParty": 135, "newModels": 17, "sources": 39 },
  "sections": {
    "models":   [ ...Item ],
    "products": [ ...Item ],
    "industry": [ ...Item ],
    "papers":   [ ...Item ],
    "tips":     [ ...Item ]
  },
  "published": true
}
```

`sections` 的 key（`models / products / industry / papers / tips`）与 `/items` 的 `category` slug 命名不同，展示时按 sections key 分组即可。

### GET /dailies

```json
{
  "dailies": [
    { "date": "2026-05-13", "headline": "Anthropic与谷歌齐发力..." },
    { "date": "2026-05-12", "headline": "AI 安全攻防战..." }
  ],
  "total": 3
}
```

## 输出规范

向用户展示时**隐藏所有 HTTP 实现细节**——不暴露端点路径、参数名、cursor token、HTTP 状态码、速率限制数字。

- 时间转北京时间，用相对表述（"2 小时前"、"今天上午"）
- 每条结果保留原文链接（`url` 字段）
- `summary` 不为 `null` 时展示摘要，为 `null` 时省略该行
- 结尾注明数据来源

### 精选动态模板

```
📡 AITOP 精选 · 过去 {时间窗} · {N} 条

{序号}. [{title}]({url})
   来源：{sourceName}  ·  {publishedAt 转北京时间，null 则省略}
   {summary 不为 null 时显示}

---
数据来自 AITOP (aitop.news)，摘要由 LLM 生成，引用前请点原链核对。
```

### 日报模板

```
📋 AITOP 日报 · {date}
{headline}

{summaryMd 渲染为 Markdown}

---
数据来自 AITOP (aitop.news)
```

## 禁止行为

| 禁止 | 原因 |
|------|------|
| 把宽泛问题路由到 `/daily` | 日报是 UTC 0 点切片，与滚动时间窗语义不符 |
| 未经用户要求使用 `mode=all` | 全量池子量大且杂，精选已够用 |
| 猜测或编造内容 | 以 API 返回为准，没有的不补充 |
| 把 `summary` 当原文引用 | 摘要由 LLM 生成，引用需回 `url` 核对 |
| 高频重复调用同一问题 | `/items` 有 60s 缓存，日报每天只更新一次 |
| 并发翻页 | cursor 翻页必须串行，`hasMore=false` 立即停止 |
| 拉全量再客户端过滤 | 用 `?q=` / `?since=` / `?category=` 服务端过滤 |
