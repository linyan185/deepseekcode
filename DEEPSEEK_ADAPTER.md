# DeepSeek Adapter

This fork keeps the existing Claude Code agent loop, tool execution, permission
model, MCP support, and sub-agent structure. The model provider layer routes
agent requests to DeepSeek through DeepSeek's Anthropic-compatible API.

## Environment

Use the launcher from the project you want the agent to work in:

```powershell
cd D:\path\to\your\workspace
"D:\projects\deepseek-code\run-deepseek.cmd"
```

The launcher preserves the current working directory and uses the DeepSeek Code
folder only as the program location.

```powershell
$env:CLAUDE_CODE_USE_DEEPSEEK = "1"
$env:DEEPSEEK_API_KEY = "sk-..."
$env:DEEPSEEK_CODE_CONFIG_DIR = "$env:USERPROFILE\.deepseek-code"
```

Optional overrides:

```powershell
$env:DEEPSEEK_BASE_URL = "https://api.deepseek.com/anthropic"
$env:DEEPSEEK_MODEL = "deepseek-v4-pro[1m]"
```

You can also use the existing model aliases:

```powershell
node dist/cli.js --model sonnet
node dist/cli.js --model opus
```

In DeepSeek mode, `sonnet`, `opus`, and `best` map to `deepseek-v4-pro`, while
`haiku` maps to `deepseek-v4-flash`. The launcher defaults to
`deepseek-v4-pro[1m]` so local context accounting, auto-compact thresholds, and
skill budgets use the 1M DeepSeek V4 Pro window. The API request strips the
`[1m]` suffix before sending the model name to DeepSeek.

## Sub-Agents

Sub-agents inherit these DeepSeek environment variables:

- `CLAUDE_CODE_USE_DEEPSEEK`
- `DEEPSEEK_API_KEY`
- `DEEPSEEK_BASE_URL`
- `DEEPSEEK_MODEL`
- `CLAUDE_CODE_DISABLE_THINKING`
- `CLAUDE_CODE_EFFORT_LEVEL`
- `DEEPSEEK_CODE_CONFIG_DIR`
- `CLAUDE_CONFIG_DIR`

This keeps spawned agents on the same DeepSeek backend as the main session.

## Local Storage Isolation

DeepSeek mode stores config, trust state, and session transcripts under
`%USERPROFILE%\.deepseek-code` by default instead of `%USERPROFILE%\.claude`.
That keeps local DeepSeek Code conversations separate from the official Claude
Code CLI and VSCode extension.

## Compatibility Boundaries

DeepSeek Code disables thinking/effort for DeepSeek mode. The current adapter
does not preserve DeepSeek `reasoning_content` across tool-call chains, so
thinking mode is kept off to avoid API 400 errors after tool calls.

The adapter strips unsupported content blocks before API calls. Native image,
screenshot, PDF/document, thinking, server-tool, and other non-text blocks are
converted into short text placeholders so the request does not fail.

Local text extraction is supported for Word `.docx` files and text-based PDFs.
PDF extraction uses the bundled PDF parser first, then falls back to the local
`pdftotext` command from Poppler when available. Scanned PDFs and image-only
PDFs still need OCR or a vision-capable tool first.

WebFetch is available for public web pages when the local network and domain
permission allow it. In DeepSeek mode the Anthropic domain preflight is skipped,
but authenticated/private pages still need a suitable local or MCP tool with
credentials.

External APIs and services can be reached through local tools, especially shell
commands such as `curl`, `git`, `gh`, `npm`, or provider CLIs. This depends on
network access, installed commands, credentials, and user permission. It is not
the same as a built-in provider-side integration.

Anthropic server-side WebSearch is not enabled for DeepSeek mode. Use WebFetch
for known URLs, or use shell/MCP tools for search providers that you configure.
