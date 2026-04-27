# DeepSeekCode

[English](README.md) | [简体中文](README_CN.md)

DeepSeekCode is a local CLI coding agent adapted from the Claude Code codebase to run against DeepSeek's Anthropic-compatible API.

It keeps the familiar terminal agent workflow: project-aware chat, tool execution, permission prompts, MCP support, sub-agents, file editing, and non-interactive `-p` mode. The adapter routes model calls to DeepSeek, isolates local config under `.deepseek-code`, disables unsupported thinking mode, and sanitizes content blocks that the current DeepSeek-compatible endpoint does not accept.

> This is an independent community fork. It is not an official DeepSeek product and is not affiliated with Anthropic.

## Status

- Builds locally with Node.js.
- Uses DeepSeek through `DEEPSEEK_API_KEY`.
- Defaults to `deepseek-v4-pro[1m]` for local context accounting.
- Keeps generated build output out of git.
- Intended for local use from source, not as an npm package release yet.

## Requirements

- Node.js 18 or newer
- npm
- A DeepSeek API key

## Install From Source

```powershell
git clone https://github.com/linyan185/deepseekcode.git deepseekcode
cd deepseekcode
npm ci --ignore-scripts
npm run check
```

`npm run check` builds `dist/cli.js` and verifies the CLI version.

## Run

Set your key for the current shell:

```powershell
$env:DEEPSEEK_API_KEY = "sk-..."
```

From the project you want DeepSeekCode to work in, run the launcher from this repository:

```powershell
cd D:\path\to\your\project
D:\path\to\deepseekcode\run-deepseek.cmd
```

You can also run a one-shot prompt:

```powershell
D:\path\to\deepseekcode\run-deepseek.cmd -p "summarize this repository"
```

The launcher preserves your current working directory, so DeepSeekCode works on the project you started it from.

## Optional Local Link

For a local command during development:

```powershell
npm link
deepseek-code --version
deepseek-code
```

If you no longer want the linked command:

```powershell
npm unlink -g deepseekcode
```

## Configuration

Common environment variables:

```powershell
$env:DEEPSEEK_API_KEY = "sk-..."
$env:DEEPSEEK_BASE_URL = "https://api.deepseek.com/anthropic"
$env:DEEPSEEK_MODEL = "deepseek-v4-pro[1m]"
$env:DEEPSEEK_CODE_CONFIG_DIR = "$env:USERPROFILE\.deepseek-code"
```

You can also copy `.env.example` if you prefer loading variables through your own shell tooling.

Model aliases are mapped for compatibility:

| Alias | DeepSeek model |
| --- | --- |
| `sonnet`, `opus`, `best` | `deepseek-v4-pro` |
| `haiku` | `deepseek-v4-flash` |

See [DEEPSEEK_ADAPTER.md](DEEPSEEK_ADAPTER.md) for adapter details and compatibility boundaries.

## What Changed In This Fork

- Adds a `deepseek` API provider path.
- Uses `DEEPSEEK_API_KEY` and `DEEPSEEK_BASE_URL`.
- Skips Anthropic OAuth/keychain auth in DeepSeek mode.
- Stores local config under `.deepseek-code` by default.
- Disables thinking/effort options for DeepSeek mode to avoid incompatible tool-call chains.
- Converts unsupported image, document, thinking, and server-tool blocks into short text placeholders before API calls.
- Skips Anthropic analytics/preconnect paths in DeepSeek mode.
- Carries DeepSeek environment variables into sub-agents.

## Privacy Notes

DeepSeekCode is a local CLI. It can read files and run commands in the workspace you approve. Model requests are sent to the configured DeepSeek-compatible API endpoint.

Do not commit API keys, local transcripts, `.env` files, build output, or personal logs. The repository `.gitignore` excludes the common local files and generated directories.

## Build Output

Generated directories are intentionally ignored:

- `dist/`
- `build-src/`
- `node_modules/`

Rebuild with:

```powershell
npm run build
```

## Legal And Attribution

This repository contains code adapted from the Claude Code package and keeps attribution to the original rights holders. No additional open-source license is granted by this repository. See [NOTICE.md](NOTICE.md).

Before redistributing, publishing packages, or using this commercially, review the upstream terms and get legal advice where needed.
