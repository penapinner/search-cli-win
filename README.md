# search-cli — Windows 安装包

> [paperfoot/search-cli](https://github.com/paperfoot/search-cli) v0.5.1 的 Windows 预编译版本
>
> search-cli 是一个搜索聚合器，聚合 11 个搜索提供商、14 种搜索模式。专为 AI Agent（如 Claude Code deep-research）设计，提供 JSON 结构化输出。

---

## 目录

- [快速安装（5 分钟）](#快速安装5-分钟)
- [配置 API Key](#配置-api-key)
- [验证安装](#验证安装)
- [对接 Claude Code deep-research](#对接-claude-code-deep-research)
- [命令行速查](#命令行速查)
- [从源码编译（进阶）](#从源码编译进阶)

---

## 快速安装（5 分钟）

### 1. 放置 search.exe

把 `search.exe` 放到一个不容易被误删的目录，例如：

```
C:\tools\search\search.exe
```

### 2. 添加到 PATH

**方法一（推荐）**：以管理员身份打开 PowerShell，运行：

```powershell
[Environment]::SetEnvironmentVariable("Path", [Environment]::GetEnvironmentVariable("Path", "User") + ";C:\tools\search", "User")
```

**方法二**：手动添加
1. 右键"此电脑" → 属性 → 高级系统设置 → 环境变量
2. 在"用户变量"中找到 `Path`，双击编辑
3. 新建 → 输入 `C:\tools\search`
4. 确定保存

### 3. 打开新的终端

```bash
search --version
```

输出应为 `search 0.5.1`。

---

## 配置 API Key

search-cli 本身不是搜索引擎，它把查询转发给第三方搜索服务，所以需要 API key。

### 最小配置（学术搜索）

学术搜索推荐使用 **serper**（访问 Google Scholar）：

```bash
search config set keys.serper 你的serper_api_key
```

或通过环境变量（更安全）：

```bash
set SEARCH_SERPER_KEY=你的serper_api_key
```

### 查看配置状态

```bash
search config check
```

### 配置文件位置

```
%APPDATA%\search\config\config.toml
```

各 API Key 申请方式见 [SEARCH_API_KEY_GUIDE.md](./SEARCH_API_KEY_GUIDE.md)。

---

## 验证安装

```bash
# 查看版本
search --version

# 检查配置状态
search config check

# 测试搜索（需要有至少一个 provider 配置了 key）
search search -q "CRISPR therapy" -m academic --json -c 5

# 查看所有 provider
search providers
```

---

## 对接 Claude Code deep-research

如果你在使用 [claude-deep-research-skill](https://github.com/199-biotechnologies/claude-deep-research-skill)，search-cli 已自动就绪。

deep-research skill 在检索阶段通过 Bash 调用 search-cli：

```bash
search "quantum computing 2025" -m academic --json -c 10
```

skill 的 `reference/methodology.md` 中定义了调用方式：
- 主搜索：`search "query" --json -c 10`
- 学术模式：`search "query" -m academic --json -c 15`
- 新闻模式：`search "query" -m news --json -c 10`
- 网页提取：`search "URL" -m extract --json`

安装 search-cli 后，deep-research 会自动优先使用它。如果 search-cli 不可用或失败，skill 会自动回退到 Claude 内置的 WebSearch。

---

## 命令行速查

```bash
# 自动检测搜索意图
search "rust error handling"

# 指定搜索模式
search search -q "CRISPR" -m academic         # 学术论文
search search -q "AI news" -m news             # 新闻
search search -q "CEO of Stripe" -m people     # 人物/LinkedIn
search search -q "quantum computing" -m deep   # 深度搜索（所有 provider）

# JSON 输出（管道到 jq 或其他工具）
search "query" --json
search search -q "query" -m academic --json | jq '.results[].url'

# 指定 provider
search search -q "query" -p brave              # 只用 Brave
search search -q "query" -p serper,exa         # 只用 Serper + Exa

# X/Twitter 搜索（需 xAI key）
search --x "trending topic"
```

### 搜索模式说明

| 模式 | 说明 | 使用的 Provider |
|------|------|----------------|
| `auto` | 自动检测（默认） | 全部 |
| `general` | 通用网页搜索 | Brave + Serper + Exa + Jina + Tavily + Perplexity |
| `academic` | 学术论文 | Exa + Serper + Tavily + Perplexity |
| `scholar` | Google Scholar | Serper |
| `patents` | 专利搜索 | Serper |
| `news` | 新闻 | Brave + Serper + Tavily + Perplexity |
| `people` | 人物搜索 | Exa |
| `social` | X/Twitter | xAI |

---

## 从源码编译（进阶）

如果需要在其他平台上编译，或想使用最新源码：

1. 安装依赖：
   - [VS BuildTools 2022](https://visualstudio.microsoft.com/visual-cpp-build-tools/)（安装"使用 C++ 的桌面开发"工作负载）
   - [LLVM](https://github.com/llvm/llvm-project/releases)（用于 libclang.dll，bindgen 需要）
   - [cmake](https://cmake.org/download/)
   - [nasm](https://www.nasm.us/)
   - [Rust](https://rustup.rs/)（stable-msvc toolchain）

2. 克隆并编译：
   ```bash
   git clone https://github.com/paperfoot/search-cli.git
   cd search-cli
   cargo build --release
   ```

3. 编译后的二进制在 `target/release/search.exe`

详细步骤见 [build_from_source/README.md](./build_from_source/README.md)。

---

## 文件清单

```
search-cli-package/
├── search.exe                  # 预编译二进制（64位，16MB）
├── config.example.toml         # 配置文件模板
├── README.md                   # 本文件
├── SEARCH_API_KEY_GUIDE.md     # API Key 申请指南
└── build_from_source/
    ├── build.bat               # Windows 构建脚本
    └── README.md               # 从源码编译说明
```

---

## License

search-cli 本身基于 MIT License。本打包发行版同样基于 MIT。
