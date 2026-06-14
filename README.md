# WinFix

WinFix is a Codex skill for diagnosing and safely resolving common Windows PC problems from a symptom.

It is designed for evidence-first support: inspect local state, explain findings, choose low-risk fixes first, and verify results. It avoids silent destructive actions.

## What It Handles

- Disk pressure, large files, temp/cache cleanup
- Memory pressure, process triage, startup issues
- Chrome, Edge, VS Code, WeChat, and arbitrary app failures
- Network, proxy, DNS, and HTTP connectivity checks
- Windows Update, drivers, devices, audio, display, printers
- WSL, Docker, Android, Flutter, Java, Node, Python, Git
- Crash, reboot, freeze, and blue screen clues from Windows event logs
- Defender and firewall status checks

## Install

Copy the `winfix/` folder into your Codex skills directory:

```powershell
Copy-Item -Recurse .\winfix "$env:USERPROFILE\.agents\skills\winfix"
```

Then ask Codex things like:

```text
帮我分析 C 盘空间
Chrome 内存占用很高
这个 API 地址访问不了
电脑刚才自动重启了
安卓开发环境能不能用
```

## Direct Script Usage

```powershell
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.agents\skills\winfix\scripts\inspect_windows.ps1" -Mode health
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.agents\skills\winfix\scripts\inspect_windows.ps1" -Mode disk -Format json
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.agents\skills\winfix\scripts\inspect_windows.ps1" -Mode net-test -Target "https://www.microsoft.com"
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.agents\skills\winfix\scripts\inspect_windows.ps1" -Mode app -ProcessName chrome
```

## Safety

The skill is read-only by default. It does not silently delete user files, unregister WSL distributions, prune Docker volumes, edit the registry, remove drivers, change security settings, or run Windows repair commands without explicit user confirmation.

High-risk areas include Downloads, Desktop, Documents, chat files, browser profiles, SSH/API keys, Docker volumes, WSL distributions, drivers, registry, BitLocker, partitions, and Windows security settings.

## Compatibility

Tested on Windows with Windows PowerShell. Some diagnostic cmdlets vary by Windows edition, PowerShell version, and permissions. The script is designed to degrade gracefully when tools are missing.

## Skill Folder

The actual skill is in:

```text
winfix/
```

Key files:

- `SKILL.md`
- `scripts/inspect_windows.ps1`
- `references/safety.md`
- `references/issue-routes.md`
- `references/release-checklist.md`
- `evals/evals.json`


