# WinFix Skill

WinFix helps Codex diagnose and safely resolve common Windows PC problems from a user-provided symptom.

## Scope

Supports read-only inspection and guided fixes for:

- disk pressure, large files, temp/cache cleanup
- memory/process pressure and startup issues
- Chrome, Edge, VS Code, WeChat, and arbitrary app failures
- network, proxy, DNS, and HTTP connectivity checks
- Windows Update, drivers, devices, audio, display, printers
- WSL, Docker, Android, Flutter, Java, Node, Python, Git
- crash, reboot, blue screen clues from event logs
- Defender and firewall status checks

## Safety model

The default workflow is inspect first, explain evidence, then choose the safest fix. The skill does not silently delete user files, unregister WSL distros, prune Docker volumes, edit the registry, remove drivers, change firewall/Defender state, or run system repair commands without explicit confirmation.

## Example commands

```powershell
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.agents\skills\winfix\scripts\inspect_windows.ps1" -Mode health
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.agents\skills\winfix\scripts\inspect_windows.ps1" -Mode disk -Format json
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.agents\skills\winfix\scripts\inspect_windows.ps1" -Mode net-test -Target "https://www.microsoft.com"
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.agents\skills\winfix\scripts\inspect_windows.ps1" -Mode app -ProcessName chrome
```

## Marketplace readiness checklist

- Valid `SKILL.md` frontmatter
- UI metadata in `agents/openai.yaml`
- Read-only diagnostics by default
- JSON output for high-use modes: `health`, `overview`, `disk`, `memory`, `dev`, `network`
- Safety reference and issue route reference
- Test prompts in `evals/evals.json`

## Known limits

- Some Windows cmdlets vary by Windows edition and PowerShell version.
- Physical hardware faults, battery swelling, liquid damage, and pre-boot failures need vendor or repair-shop diagnostics.
- Admin repair commands are provided as guidance and should be run only after confirmation.

