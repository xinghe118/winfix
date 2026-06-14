# WinFix

WinFix 是一个用于 Windows 电脑维护的 Codex skill。你只需要描述遇到的问题，它会先检查本机状态，再根据证据给出清理、修复或优化建议。

它的原则是“先诊断，再处理”：先收集磁盘、内存、网络、驱动、事件日志等信息，再解释问题原因，优先选择低风险方案，并在操作后做验证。默认不会静默删除用户文件，也不会直接修改系统关键设置。

## 能解决什么

- C 盘空间不足、大文件定位、临时文件和缓存清理
- 内存占用高、进程排查、开机启动项分析
- Chrome、Edge、VS Code、微信以及普通软件卡顿或打不开
- 网络、代理、DNS、HTTP 连通性检查
- Windows 更新、驱动、设备、声音、显示器、打印机问题
- WSL、Docker、Android、Flutter、Java、Node、Python、Git 开发环境检查
- 蓝屏、自动重启、卡死、闪退等事件日志线索分析
- Defender 和防火墙状态检查

## 安装

从 GitHub 安装：

```powershell
npx skills add xinghe118/winfix -g
```

也可以手动复制到 Codex skills 目录：

```powershell
Copy-Item -Recurse . "$env:USERPROFILE\.agents\skills\winfix"
```

安装后，可以这样问 Codex：

```text
帮我分析 C 盘空间
Chrome 内存占用很高
这个 API 地址访问不了
电脑刚才自动重启了
安卓开发环境能不能用
```

## 直接运行脚本

```powershell
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.agents\skills\winfix\scripts\inspect_windows.ps1" -Mode health
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.agents\skills\winfix\scripts\inspect_windows.ps1" -Mode disk -Format json
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.agents\skills\winfix\scripts\inspect_windows.ps1" -Mode net-test -Target "https://www.microsoft.com"
powershell -ExecutionPolicy Bypass -File "$env:USERPROFILE\.agents\skills\winfix\scripts\inspect_windows.ps1" -Mode app -ProcessName chrome
```

## 示例输出

### C 盘空间分析

用户可以直接说：

```text
帮我分析 C 盘空间，看看哪些可以清理
```

WinFix 会先检查磁盘和常见缓存目录，再按风险给出建议。典型输出类似：

```text
结论：C 盘剩余空间偏低，建议优先清理低风险临时目录。

证据：
- C: 剩余 14.2%，状态：watch
- C:\Users\...\AppData\Local\Temp：956.67 MB，风险：低
- C:\Windows\Temp：437.85 MB，风险：低
- pnpm / pip / VS Code 缓存体积较小，可按需清理

建议：
1. 先清理 Temp 和 Windows Temp。
2. 不要直接删除 Downloads、Desktop、Documents、微信文件。
3. 清理后重新运行磁盘检查验证释放空间。
```

### Chrome 内存排查

用户可以直接说：

```text
Chrome 内存占用很高，帮我检查一下
```

WinFix 会检查 Chrome/Edge 进程数量、内存占用和浏览器缓存候选目录。典型输出类似：

```text
结论：Chrome 内存高通常来自多标签页、多进程架构、扩展或缓存膨胀。

证据：
- chrome 进程数量较多
- 排名前几的 chrome 子进程占用内存较高
- 浏览器 Cache / Code Cache / GPUCache 可作为低风险清理候选

建议：
1. 先关闭不用的标签页和窗口。
2. 如需清理，只清理缓存，不清理密码、Cookie 和浏览器 Profile。
3. 如果仍然异常，再检查扩展或用无扩展模式启动。
```

### 网络代理检测

用户可以直接说：

```text
这个 API 地址访问不了：https://www.microsoft.com
```

WinFix 会检查环境变量代理、WinHTTP 代理、DNS 和 HTTP 响应。代理中包含账号密码时会自动脱敏。

```text
结论：网络诊断会优先区分 DNS、代理、TLS、HTTP 状态码和远端服务错误。

证据：
- HTTP_PROXY / HTTPS_PROXY 会显示为 http://***:***@host:port
- DNS 能解析表示域名解析正常
- curl -I 返回 200/301/403/500 等状态码，用于判断问题位置

建议：
1. 如果是 DNS 失败，先检查网络和 DNS。
2. 如果是代理失败，先确认代理地址和端口。
3. 如果是 5xx，优先判断为远端服务问题，不盲目改本机配置。
```

## 安全边界

默认情况下，WinFix 只做只读检查。除非用户明确确认，否则它不会删除用户文件、注销 WSL 发行版、清理 Docker 卷、修改注册表、卸载驱动、调整安全设置，也不会运行系统修复命令。

高风险区域包括：下载、桌面、文档、聊天文件、浏览器配置、SSH/API 密钥、Docker 卷、WSL 发行版、驱动、注册表、BitLocker、磁盘分区和 Windows 安全设置。

可以直接清理的范围很窄，主要是低风险临时目录；涉及用户文件、浏览器登录状态、聊天记录、Docker 卷、WSL 数据、驱动、注册表和系统修复命令时，必须先解释影响并取得明确确认。

## 兼容性

已在 Windows PowerShell 环境下测试。部分诊断命令会受到 Windows 版本、PowerShell 版本和权限影响；如果某些工具不可用，脚本会尽量降级处理，而不是直接中断。

## 主要文件

- `SKILL.md`
- `scripts/inspect_windows.ps1`
- `references/safety.md`
- `references/issue-routes.md`
- `references/release-checklist.md`
- `evals/evals.json`


