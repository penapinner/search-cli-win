# API Key 申请指南

search-cli 需要 API key 来调用各个搜索服务。以下是最常用的几个服务的申请方式、费用和免费额度。

---

## 学术搜索首选

### Serper — Google Scholar + Patents 搜索

- **网址**：https://serper.dev
- **注册**：Google 账号登录
- **免费额度**：50 次/月（无需绑卡）
- **付费**：$50/月起
- **模式**：`scholar`（Google Scholar）、`patents`（专利）、`general`（网页）、`news`（新闻）、`images`（图片）、`places`（本地）
- **推荐理由**：学术搜索的最佳选择，直接返回 Google Scholar 结果

配置：
```bash
search config set keys.serper 你的key
```

### Tavily — AI 原生搜索（含 Academic 模式）

- **网址**：https://tavily.com
- **注册**：邮箱或 Google 账号
- **免费额度**：1000 次/月（无需绑卡）
- **付费**：$20/月起
- **模式**：`general`、`news`、`academic`、`deep`
- **推荐理由**：免费额度多，专为 LLM Agent 设计，响应快

配置：
```bash
search config set keys.tavily 你的key
```

### Exa — 语义/神经网络搜索

- **网址**：https://exa.ai
- **注册**：邮箱或 Google 账号
- **免费额度**：1000 次/月
- **付费**：$25/月起
- **能力**：按语义搜索（而非关键词）、找相似文章、LinkedIn 人物搜索
- **推荐理由**：适合"找和这篇论文相似的研究"

配置：
```bash
search config set keys.exa 你的key
```

---

## 通用搜索（可选）

### Brave Search

- **网址**：https://brave.com/search/api/
- **免费额度**：2000 次/月（需注册 Free plan）
- **付费**：$5/月起
- **模式**：`general`、`news`
- **特点**：独立搜索引擎（不依赖 Google/Bing），隐私友好

配置：
```bash
search config set keys.brave 你的key
```

### Jina AI

- **网址**：https://jina.ai/reader/
- **免费额度**：有免费层
- **能力**：网页搜索 + URL 转 Markdown
- **模式**：`general`、`extract`、`scrape`

配置：
```bash
search config set keys.jina 你的key
```

### Perplexity Sonar

- **网址**：https://docs.perplexity.ai/
- **付费**：按量计费
- **能力**：AI 生成答案 + 引用来源

配置：
```bash
search config set keys.perplexity 你的key
```

### Firecrawl

- **网址**：https://www.firecrawl.dev/
- **免费额度**：500 次
- **能力**：JS 渲染页面抓取、结构化提取
- **模式**：`extract`、`scrape`

配置：
```bash
search config set keys.firecrawl 你的key
```

### xAI (Grok / Twitter)

- **网址**：https://console.x.ai
- **付费**：按量计费
- **能力**：搜索 X/Twitter 内容
- **模式**：`social`

配置：
```bash
search config set keys.xai 你的key
```

---

## 推荐配置方案

### 方案 A：纯学术搜索（最低成本）

```
serper  → Google Scholar + 专利
tavily  → 学术论文通用搜索
```

免费额度：serper 50次/月 + tavily 1000次/月，日常使用足够。

### 方案 B：全面搜索（推荐）

```
serper    → 学术 (Scholar/Patents)
tavily    → 通用 + 学术 + 新闻
brave     → 通用网页搜索（量大）
exa       → 语义相似搜索
```

### 方案 C：完全体

所有 11 个 provider 都配好，`-m deep` 模式自动调用全部。

---

## 配置方式

### 方式一：命令行设置（推荐）

```bash
search config set keys.serper 你的key
search config set keys.tavily 你的key
```

配置保存在 `%APPDATA%\search\config\config.toml`。

### 方式二：环境变量（更安全，不落盘）

| Key | 环境变量 |
|-----|---------|
| brave | `SEARCH_BRAVE_KEY` |
| serper | `SEARCH_SERPER_KEY` |
| exa | `SEARCH_EXA_KEY` |
| tavily | `SEARCH_TAVILY_KEY` |
| jina | `SEARCH_JINA_KEY` |
| firecrawl | `SEARCH_FIRECRAWL_KEY` |
| perplexity | `SEARCH_PERPLEXITY_KEY` |
| xai | `SEARCH_XAI_KEY` |

```bash
set SEARCH_SERPER_KEY=你的key
set SEARCH_TAVILY_KEY=你的key
```

### 方式三：直接编辑配置文件

编辑 `%APPDATA%\search\config\config.toml`：

```toml
[keys]
serper = "你的key"
tavily = "你的key"

[settings]
timeout = 10
count = 10
```

---

## 验证

```bash
# 查看哪些 provider 已配置
search config check

# 测试学术搜索
search search -q "machine learning transformers" -m academic --json -c 5

# 测试 Scholar 搜索（需 serper）
search search -q "deep learning" -m scholar --json -c 5
```
