# Quickstart

## 1. Install

```powershell
git clone <repo-url> deepseekcode
cd deepseekcode
npm ci --ignore-scripts
npm run check
```

## 2. Set Your API Key

```powershell
$env:DEEPSEEK_API_KEY = "sk-..."
```

## 3. Start In A Project

Run the launcher from the project you want to edit:

```powershell
cd D:\path\to\your\project
D:\path\to\deepseekcode\run-deepseek.cmd
```

## 4. One-Shot Mode

```powershell
D:\path\to\deepseekcode\run-deepseek.cmd -p "explain the main architecture"
```

## 5. Useful Checks

```powershell
npm run build
node dist\cli.js --version
```

For adapter details, see [DEEPSEEK_ADAPTER.md](DEEPSEEK_ADAPTER.md).
