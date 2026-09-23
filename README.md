# AITOP Skills

AITOP（[aitop.news](https://aitop.news)）的官方 Skill 集合：一个全能入口，加 16 个只干一件事的专用入口。
SKILL.md 标准格式，Claude Code、Codex CLI、Cursor、Gemini CLI、GitHub Copilot、OpenCode、Cline、Windsurf 等任意 Agent 都能装。
匿名免费，无需 API Key。

在线矩阵与一键复制：https://aitop.news/skills

## 安装

**方式一：让 Agent 自己装。** 把下面这句发给你的 Agent（换成你要的那个）：

```
帮我安装这个 skill：https://aitop.news/aitop-skill/
帮我安装这个 skill：https://aitop.news/skills/ai-news/
```

**方式二：手动。** 把 `skills/<名字>/` 整个目录复制到 Agent 的 skills 目录（Claude Code 是 `~/.claude/skills/`）。

根目录的 `SKILL.md` 就是全能入口 `aitop-skill`，与 `skills/aitop-skill/SKILL.md` 相同。

## 全能入口

| Skill | 名称 | 做什么 | 适合 |
|---|---|---|---|
| [`aitop-skill`](skills/aitop-skill/SKILL.md) | AITOP 资讯助手 | 全功能入口：动态、日报、搜索、详情、来源核对，一个装完。 | 所有人 |

只想装一个就装它。它覆盖资讯流这组的全部能力；查模型价格、融资、政策这些表，或看热度榜，要装对应的专用入口。

## 专用入口

按母版分组：同一母版的 skill 共用一套接入与输出规则，只换查询和格式。

### 资讯流（9）

按时间窗读 AI 动态，每个入口收窄到一类内容。

| Skill | 名称 | 做什么 | 适合 |
|---|---|---|---|
| [`ai-news`](skills/ai-news/SKILL.md) | AI 新闻速览 | 「今天 AI 圈有什么」——只做这一件事，5 秒给结论。 | 所有人 · 最高频 |
| [`ai-daily`](skills/ai-daily/SKILL.md) | AI 日报 | 每天一份人工编排的精编日报，有主题、有结构。 | 习惯固定时间读一份的人 |
| [`ai-models`](skills/ai-models/SKILL.md) | 大模型发布追踪 | 只看模型：谁发布了、什么参数、跑分如何。 | 开发者 · 技术决策者 |
| [`ai-papers`](skills/ai-papers/SKILL.md) | AI 论文速递 | 只看论文：这周有什么值得读的研究。 | 研究者 · 学生 |
| [`ai-tools`](skills/ai-tools/SKILL.md) | AI 工具发现 | 只看产品：又有什么新东西可以拿来用了。 | 产品经理 · 工具党 |
| [`ai-funding`](skills/ai-funding/SKILL.md) | AI 投融资与政策 | 只看钱和规则：谁融了多少，政策往哪走。 | 投研 · 创业者 |
| [`ai-weekly`](skills/ai-weekly/SKILL.md) | AI 一周回顾 | 一周错过的都在这，按主题归好类。 | 周末补课的人 |
| [`ai-labs`](skills/ai-labs/SKILL.md) | 大厂一手动态 | 只看官方发布，不看二手转述。 | 要准确信源的人 |
| [`ai-topics`](skills/ai-topics/SKILL.md) | AI 内容选题雷达 | 不只是给你新闻，是告诉你这条能怎么写。 | 自媒体作者 · 运营 |

### 话题雷达（2）

不按时间按话题：什么在涨，一条线的来龙去脉。

| Skill | 名称 | 做什么 | 适合 |
|---|---|---|---|
| [`ai-rising`](skills/ai-rising/SKILL.md) | AI 上升热榜 | 不按时间按增速：哪些词正在往上冲。 | 追热点的人 · 运营 |
| [`ai-topic`](skills/ai-topic/SKILL.md) | AI 话题深挖 | 给一个词，还你它的来龙去脉、共现话题和信源。 | 研究者 · 分析师 · 写深度稿的人 |

### 数据台（4）

查结构化的表：模型参数价格、融资记录、政策状态、周榜。

| Skill | 名称 | 做什么 | 适合 |
|---|---|---|---|
| [`ai-model-db`](skills/ai-model-db/SKILL.md) | 大模型参数与价格库 | 查规格不查新闻：参数、上下文、API 价格、开源协议。 | 开发者 · 做选型的人 |
| [`ai-funding-db`](skills/ai-funding-db/SKILL.md) | AI 融资数据表 | 一笔一行：谁、哪一轮、多少钱、谁投的。 | 投研 · 创业者 · BD |
| [`ai-policy-desk`](skills/ai-policy-desk/SKILL.md) | AI 政策台 | 每条政策一张卡：状态、生效日、影响谁、该做什么。 | 合规 · 出海 · 政策研究 |
| [`ai-rank`](skills/ai-rank/SKILL.md) | AI 周榜 | 按周排好的四张榜：东盟、中国、全球、论文。 | 每周复盘的人 |

### 简报（1）

一次读几张表，拼成一份分节简报。

| Skill | 名称 | 做什么 | 适合 |
|---|---|---|---|
| [`ai-asean`](skills/ai-asean/SKILL.md) | 东南亚 AI 简报 | 一次拿到东盟 AI 的榜单、融资和政策。 | 出海东南亚 · 关注东盟市场的人 |

## 怎么选

- 别全装：每个 skill 都会占 Agent 的上下文，装 2–3 个就够。
- 典型组合：开发者 `ai-news` + `ai-model-db`；研究者 `ai-papers` + `ai-topic`；自媒体 `ai-topics` + `ai-rising`；投研 `ai-funding-db` + `ai-policy-desk`；出海东南亚 `ai-asean` + `ai-policy-desk`。

## 说明

- 本仓库由 AITOP 主仓库生成后同步，Skill 版本 `1.5`。以 https://aitop.news/skills 上的版本为准。
- 摘要由 AI 生成，引用具体事实请回到来源原文核对。
- 反馈：https://aitop.news/feedback · yuge@aitop.news
