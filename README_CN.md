# DeepSeekCode

[English](README.md) | [简体中文](README_CN.md)

DeepSeekCode 是一个本地命令行编程代理。它基于 Claude Code 代码库改造，让模型请求走 DeepSeek 的 Anthropic-compatible API。

它保留了终端代理常用体验：项目内对话、工具执行、权限确认、MCP、子代理、文件编辑、以及 `-p` 非交互模式。这个 fork 主要做了 DeepSeek provider 适配、本地配置目录隔离、禁用当前不兼容的 thinking 链路，并在请求前清理 DeepSeek 兼容接口不支持的内容块。

> 这是社区独立 fork，不是 DeepSeek 官方产品，也不隶属于 Anthropic。

## 当前状态

- 可以用 Node.js 在本地构建。
- 通过 `DEEPSEEK_API_KEY` 使用 DeepSeek。
- 默认使用 `deepseek-v4-pro[1m]` 做本地上下文预算。
- 构建产物不进入 git。
- 当前定位是从源码本地运行，还不是正式 npm 发包。

## 环境要求

- Node.js 18 或更高版本
- npm
- DeepSeek API key

## 从源码安装

```powershell
git clone https://github.com/linyan185/deepseekcode.git deepseekcode
cd deepseekcode
npm ci --ignore-scripts
npm run check
```

`npm run check` 会构建 `dist/cli.js`，并检查 CLI 版本输出。

## 运行

先在当前 shell 设置 API key：

```powershell
$env:DEEPSEEK_API_KEY = "sk-..."
```

然后进入你希望 DeepSeekCode 操作的项目目录，从那里调用本仓库里的启动器：

```powershell
cd D:\path\to\your\project
D:\path\to\deepseekcode\run-deepseek.cmd
```

也可以用一次性命令：

```powershell
D:\path\to\deepseekcode\run-deepseek.cmd -p "summarize this repository"
```

启动器会保留当前工作目录，所以它操作的是你启动时所在的项目，而不是 DeepSeekCode 源码目录。

## 可选：本地链接命令

开发时可以把命令链接到全局：

```powershell
npm link
deepseek-code --version
deepseek-code
```

不需要时移除：

```powershell
npm unlink -g deepseekcode
```

## 配置

常用环境变量：

```powershell
$env:DEEPSEEK_API_KEY = "sk-..."
$env:DEEPSEEK_BASE_URL = "https://api.deepseek.com/anthropic"
$env:DEEPSEEK_MODEL = "deepseek-v4-pro[1m]"
$env:DEEPSEEK_CODE_CONFIG_DIR = "$env:USERPROFILE\.deepseek-code"
```

如果你习惯用自己的 shell 工具加载环境变量，也可以参考 `.env.example`。

为了兼容原来的模型别名，当前映射如下：

| 别名 | DeepSeek 模型 |
| --- | --- |
| `sonnet`, `opus`, `best` | `deepseek-v4-pro` |
| `haiku` | `deepseek-v4-flash` |

更多适配细节见 [DEEPSEEK_ADAPTER.md](DEEPSEEK_ADAPTER.md)。

## 这个 Fork 做了什么

- 增加 `deepseek` API provider 路径。
- 使用 `DEEPSEEK_API_KEY` 和 `DEEPSEEK_BASE_URL`。
- DeepSeek 模式下跳过 Anthropic OAuth/keychain 认证。
- 默认把本地配置保存到 `.deepseek-code`。
- DeepSeek 模式下禁用 thinking/effort，避免工具调用链里出现兼容性问题。
- 请求前把 image、document、thinking、server-tool 等不支持的内容块转成简短文本占位。
- DeepSeek 模式下跳过 Anthropic analytics/preconnect 相关路径。
- 子代理继承 DeepSeek 相关环境变量。

## 隐私提醒

DeepSeekCode 是本地 CLI。它会在你批准后读取文件和执行命令，并把模型请求发送到你配置的 DeepSeek-compatible API endpoint。

不要提交 API key、本地会话记录、`.env`、构建产物或个人日志。仓库的 `.gitignore` 已经排除了常见本地文件和生成目录。

## 构建产物

这些目录是生成产物，不进入 git：

- `dist/`
- `build-src/`
- `node_modules/`

重新构建：

```powershell
npm run build
```

## 法律与归属

本仓库包含从 Claude Code 包改造而来的代码，并保留对原权利方的归属说明。本仓库不额外授予开源许可证。详见 [NOTICE.md](NOTICE.md)。

在再次分发、发布包或商业使用前，请先确认上游条款；必要时请咨询专业法律意见。
