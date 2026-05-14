# browser — isolated site launchers

**用途**：给容易受 Cloudflare / Cookie / 会话状态影响的网站提供独立浏览器 profile 启动入口，避免污染默认浏览器 profile。

**脚本路径**：`.agents/scripts/browser/`

| 脚本 | 作用 |
|------|------|
| `start-linuxdo-edge-isolated.ps1` | 使用独立 Edge `user-data-dir` 启动 `linux.do`，可选只清该独立 profile 下的 `linux.do` 单站点数据 |

**调用方式**：
```powershell
.\.agents\scripts\browser\start-linuxdo-edge-isolated.ps1
```

只清理独立 profile 里的 `linux.do` 站点数据时：
```powershell
.\.agents\scripts\browser\start-linuxdo-edge-isolated.ps1 -ResetLinuxDoSiteData -HeadlessResetOnly
```

**维护状态**：活跃
