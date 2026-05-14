# autopull — 自动 git pull

**用途**：后台静默定时执行 git pull，保持本地 trade_cloud 代码与远端同步。

**使用场景**：长期运行的开发/测试环境，需要代码自动更新时。

**脚本路径**：`.agents/scripts/autopull/`

| 脚本 | 作用 |
|------|------|
| `auto-git-pull.ps1` | 核心 pull 脚本 |
| `auto-git-pull-hidden.vbs` | 后台静默运行（无窗口） |
| `setup-auto-pull.ps1` / `.bat` | 安装为启动项 |
| `install-startup.ps1` | 注册开机自启 |

**维护状态**：活跃（根目录旧版 `auto-git-pull*.ps1|vbs` 已移入 `_cleanup/`，当前本体在 `.agents/scripts/autopull/`）

**注意：卸载时须同步清理 Windows 启动项和计划任务**

安装脚本（`install-startup.ps1`）会在 Windows 启动文件夹写入快捷方式：

```
%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup\AutoGitPull.lnk
```

安装脚本（`setup-auto-pull.ps1`）还会创建计划任务：

```
AutoGitPull-TradeCloud
```

移动或删除脚本后，若不删除旧快捷方式或旧计划任务，后续仍可能弹出"无法找到脚本文件"错误。

卸载步骤：
```powershell
Remove-Item "$env:APPDATA\Microsoft\Windows\Start Menu\Programs\Startup\AutoGitPull.lnk"
schtasks /delete /tn "AutoGitPull-TradeCloud" /f
```
