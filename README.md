🎮 PS5 Console Payload 管理器
PS5 Console 是一个面向 PS5 的 Payload、游戏和存档综合管理器。它将四个核心组件合并到一个 ELF 中，并统一管理外部 etaHEN 插件、Payload 和 ELF。
PS5 Console is an all-in-one manager for PS5 Payloads, Games, and Saves. It merges four core components into a single ELF and manages external etaHEN plugins, payloads, and ELFs.
当前 Web UI 版本 / Current Web UI Version: 6.1.0

📦 核心组件 / Core Components
组件 / Component	功能描述 / Description
ShadowMountPlus	游戏镜像扫描、注册和挂载。Game image scanning, registration, and mounting.
Garlic SaveMgr	PS4/PS5 存档浏览、解密、加密、重签、导入及云备份。Save browsing, decryption, encryption, resigning, importing, and cloud backup.
ftpsrv	PS5 FTP 文件服务。PS5 FTP file service.
Ghostcontrol	USB 手柄兼容与控制功能。USB controller compatibility and control features.

📜 主要改动总览 / Changelog Overview
•四合一合并 (All-in-One Merge): 将 ShadowMountPlus、GhostControl、Garlic Save Manager、ftpsrv 合并为一个可加载 ELF，统一使用 PS5 Console 品牌和中文通知。Merged ShadowMountPlus, GhostControl, Garlic Save Manager, and ftpsrv into a single loadable ELF with unified PS5 Console branding.
•统一端口 (Unified Port): Web 端口统一为 7777。Web port unified to 7777.
•UI 优化 (UI Optimization): 桌面端改为左侧导航，手机端使用可滚动汉堡菜单。Desktop uses a fixed left sidebar; mobile uses a scrollable hamburger menu.
•主题切换 (Theme Switching): 默认使用 PS5 蓝色视觉风格，支持白、黑、绿主题并可保存选择。Default PS5 blue theme, supporting White, Black, and Green themes with saved preferences.
•冲突解决 (Conflict Resolution): 解决了 -backpork 与 Ghostcontrol 的冲突问题。Resolved conflicts between -backpork and Ghostcontrol.
•状态卡片 (Status Cards): 首页提供模块即时启动、重启、禁用状态保存和鲜艳的绿色/红色状态卡片。Home page features instant start/restart, disable state saving, and vivid green/red status cards.
•游戏管理增强 (Game Management Enhanced): ShadowMountPlus 改为网页手动扫描，避免持续扫描扩展硬盘；增加封面式管理、搜索及 PKG 卸载。ShadowMountPlus switched to manual web scanning to reduce disk activity; added cover-grid management, search, and PKG uninstall.
•存档工具整合 (Save Tools Integrated): 统一到一个菜单，补充手机滚动和下拉菜单遮挡修复。Unified into one menu with fixes for mobile scrolling and dropdown遮挡.
•云备份功能 (Cloud Backup): 增加游戏关闭后的存档变化监视、近期同步记录和 FTP 增量上传。Added save change monitoring after game close, recent sync logs, and FTP incremental upload.
•插件管理 (Plugin Management): 扫描 etaHEN 插件、Payload 和 ELF，支持启动、重启、启用及随机启动配置。Scans etaHEN plugins, payloads, and ELFs, supporting start, restart, enable, and random start configurations.
•日志统一 (Unified Logging): 所有组件日志统一写入 /data/sgfcnlog/。All component logs are unified to /data/sgfcnlog/.

📅 更新动态 (7.12) / Update Log (7.12)
1. GhostControl / USB 手柄修复 (USB Controller Fixes)
•修复待机问题: 修复待机后 USB 虚拟手柄未完全释放，导致 PS5 提示“无法连接更多蓝牙手柄”的问题。待机前自动暂停 GhostControl，并释放 USB endpoint 和虚拟手柄。Fix Standby Issue: Fixed virtual USB controller not fully releasing after standby, causing “Cannot connect more Bluetooth controllers” errors. GhostControl now pauses automatically before standby.
•优化扫描逻辑: 唤醒后不再自动反复扫描 USB 手柄，避免重复创建虚拟设备。增加系统级单实例锁，阻止 PS5 Console 被重复注入。Optimize Scan Logic: No longer auto-scans repeatedly after wake to avoid duplicate virtual devices. Added system-level single-instance lock to prevent duplicate injection.
•新增手动扫描: 控制中心新增“扫描 USB 手柄”按钮，支持中英文状态反馈。开机后的新 USB 手柄改为通过 Web UI 手动扫描。Manual Scan Added: Added “Scan USB Controllers” button in Control Center. New USB controllers after boot must be scanned manually via Web UI.
•修复游戏内控制: 修复首次进入游戏后 USB 手柄无法控制的问题。检测到游戏启动后延迟约 1.2 秒，自动将现有虚拟手柄重新绑定到当前用户。Fix In-Game Control: Fixed USB controller unresponsiveness on first game launch. Automatically rebinds existing virtual controllers to the current user with a ~1.2s delay after game launch.
•性能优化: 移除高频 VDI 成功日志，停止每两秒持续枚举 USB，降低 USB 总线、CPU 和日志写入压力。Performance: Removed high-frequency VDI success logs and stopped 2-second USB enumeration intervals to reduce bus/CPU load.
2. 游戏扫描与挂载逻辑 (Game Scanning & Mounting)
•调整自动扫描: PS5 每次完整开机后，Payload 首次启动自动扫描并挂载一次。同一次开机内重新加载 Payload 不自动扫描。待机唤醒后不自动扫描。其他扫描操作全部由 Web UI 手动触发。Auto-Scan Adjustment: Auto-scans once on first payload launch after full boot. Does not auto-scan on payload reload or standby wake. Other scans are manual via Web UI.
•修复扫描错误: 修复待机清理游戏挂载后，重复加载 Payload 可能错误执行自动扫描的问题。Fix Scan Error: Fixed issue where reloading payload after standby cleanup might trigger incorrect auto-scans.
3. 游戏标题广告净化 (Title Ad Purification)
•自动净化: 扫描时自动删除标题末尾的网站域名广告（例如 Metro Exodus-2468c.com → Metro Exodus）。Auto-Purify: Automatically removes domain ads from title endings during scan (e.g., Metro Exodus-2468c.com → Metro Exodus).
•同步修正: 同步修正 app.db 中已经注册的污染标题。新安装游戏的 param.json 副本也会自动净化。Sync Fix: Corrects polluted titles in app.db. New game param.json copies are also purified.
•规则说明: 规则只处理标题末尾明显的域名，保留正常数字、版本号及标题内容。Rule: Only removes obvious domains at the end; keeps normal numbers, versions, and title content.
4. 日志和配置文件整理 (Log & Config Organization)
•路径统一: 所有日志与配置文件统一迁移到 /data/PS5Console/。首次启动新版时自动迁移旧配置和日志。Path Unification: All logs and configs moved to /data/PS5Console/. Old configs migrate automatically on first launch.
•新增功能: 控制中心终端区域新增“清空日志”按钮，支持中英文，可清理当前日志及轮转日志。New Feature: Added “Clear Logs” button in Control Center terminal (supports CN/EN).
5. Web UI 修改 (Web UI Changes)
•名称变更: “Payload 管理管理”统一改名为“控制中心”。Rename: “Payload Management” renamed to “Control Center”.
•语言切换: 高级设置新增中文 / English 界面语言切换，通过浏览器 localStorage 保存。Language Switch: Added CN/EN toggle in Advanced Settings, saved via browser localStorage.
•UI 细节: 补齐英文模式下的主要界面、动态状态、设置说明及终端日志翻译。风扇温度控制卡片的“设置”按钮现在会自动滚动到风扇设置区域。UI Details: Completed EN translations. Fan control “Settings” button now auto-scrolls to the fan section.
•温度仪表: “扫描新游戏”上方新增 CPU、GPU 双温度仪表（仅在风扇温度控制开启时显示）。Temp Gauges: Added CPU/GPU temp gauges above “Scan New Games” (visible when fan control is on).
6. 风扇与性能问题检查 (Fan & Performance Check)
•移除调试信息: 确认原合并版会周期性输出风扇调试信息，现已移除。Remove Debug: Removed periodic fan debug output from the original merged version.
•性能警告: 日志曾记录 CPU 温度达到约 86°C、GPU 达到约 70°C，存在热降频风险。Performance Warning: Logs showed CPU ~86°C and GPU ~70°C, risking thermal throttling.
•精简热路径: GhostControl 原有的高频 USB 枚举、输入日志和周期检查也可能造成卡顿，相关热路径已精简。Optimize Hot Path: 精简ed high-frequency USB enumeration and input logs in GhostControl that caused lag.
•kstuff 建议: etaHEN 已集成 kstuff，不建议同时单独加载另一份 kstuff-lite，避免重复内核补丁导致卡顿、冻结或系统错误。kstuff Advice: etaHEN includes kstuff. Do not load kstuff-lite separately to avoid kernel conflicts and freezes.
7. 构建与最终文件 (Build & Files)
所有修改均已使用上传的 PS5 Payload SDK 重新编译，并生成完整合并版：All modifications recompiled with PS5 Payload SDK into a full merged version: * ShadowMountPlus * GhostControl * Garlic Save Manager * ftpsrv * fan-control

🚀 快速开始 / Quick Start
1.将最终 ELF 注入或加载到 PS5。Inject or load the final ELF into the PS5.
2.启动通知显示 PS5 Console 已启动 后，在同一局域网设备打开：Once the notification “PS5 Console Started” appears, open on a device in the same LAN:
 http://<PS5-IP>:7777/
3.桌面浏览器使用固定左侧菜单；手机使用顶部汉堡菜单。Desktop uses fixed left sidebar; mobile uses hamburger menu.
4.默认界面使用 PS5 蓝色主题。可在首页底部切换 PS5 / 白 / 黑 / 绿。Default theme is PS5 Blue. Switch between PS5 / White / Black / Green at the bottom of the home page.
注意 / Note: 旧版本保存过黑色主题时，新版首次打开会自动迁移为 PS5 蓝色，之后继续记住所选主题。If Black theme was saved in old versions, the first launch of the new version will migrate to PS5 Blue, then remember your choice thereafter.

🖥️ 界面和菜单 / Interface & Menus
Payload 管理首页 / Payload Management Home
首页提供大按钮卡片：Home page features large button cards: * 扫描新游戏 / Scan New Games: 手动触发 ShadowMountPlus 扫描。Manually trigger ShadowMountPlus scan. * ftpsrv: 立即启动或保存为禁用。Start immediately or save as disabled. * Ghostcontrol: 立即启动或保存为禁用。Start immediately or save as disabled. * etaHEN plugins / payloads / ELF: 扫描配置目录并显示插件卡片。Scan config directories and display plugin cards.
模块开启时卡片显示鲜艳绿色，关闭时显示红色。重启/启动 会重新请求启动对应模块。关闭模块主要保存 enabled=false，使其下次不自动启动。Cards show vivid green when active, red when inactive. Restart/Start requests a module restart. Disabling saves enabled=false to prevent auto-start next time.
存档工具 / Save Tools
左侧的“存档工具”包含：The “Save Tools” sidebar includes: * 浏览 / Browse: 查看存档、挂载、下载、删除和文件管理。View, mount, download, delete, and file management. * 解密 / Decrypt: 导出可编辑的解密存档。Export decrypted saves for editing. * 加密 / Encrypt: 把处理后的文件重新生成加密存档。Regenerate encrypted saves from processed files. * 重签 / Resign: 修改存档账户归属。Modify save account ownership. * 导入 / Import: 导入加密或解密存档。Import encrypted or decrypted saves.
支持 PS4、PS5、本机存档和 USB 存档。执行覆盖、删除、重签前应先备份。Supports PS4, PS5, local, and USB saves. Always backup before overwriting, deleting, or resigning.
游戏管理 / Game Management
游戏管理使用封面网格，支持按游戏名、Title ID 和源路径搜索。Game management uses a cover grid, supporting search by Name, Title ID, and Source Path. * 来源判断规则 / Source Logic: * 扫描到 .exfat、.ffpfsc、.ffpkg 或 .phu 源文件时，优先标记为 ShadowMountPlus 挂载。Priority given to ShadowMountPlus Mount if source files (.exfat, .ffpfsc, etc.) are found. * 未匹配到 ShadowMountPlus 源文件的可卸载 app.db 项标记为 PKG 安装。Items in app.db without source files are marked as PKG Install. * 扫描目录、完整源路径和 Title ID 会去重，避免同一游戏重复出现。Deduplicates by scan root, full path, and Title ID to avoid duplicates. * 操作菜单 / Actions: 每张卡右上角的三点菜单提供操作：Three-dot menu on cards offers: * 卸载 PKG / Uninstall PKG: 卸载游戏本体，不删除系统存档。Uninstalls game body, keeps system saves. * 删除挂载文件 / Delete Mount File: 永久删除 ShadowMountPlus 源文件，此操作不可恢复。Permanently deletes ShadowMountPlus source file (irreversible).

⚙️ 模块和 ELF 管理 / Module & ELF Management
高级设置可以分别配置 PS5 Console Web、ftpsrv 和 Ghostcontrol：Advanced settings allow individual configuration of PS5 Console Web, ftpsrv, and Ghostcontrol: * 启用 / Enable: Payload 启动时直接启动该模块。Starts module directly when Payload launches. * 随机 / Random: 仅在启用时参与随机启动逻辑。Participates in random start logic only if enabled.
只勾选“启用”而不勾选“随机”，表示每次直接启动。首页按钮用于当前会话立即启动和保存启用状态。Checking “Enable” but not “Random” means direct start every time. Home buttons control immediate start and save state for the current session.
默认扫描目录包括：Default scan directories include:
/data/etaHEN/plugins
/data/etaHEN/payloads
网页会扫描插件、Payload 和 ELF，并把启动或重启请求发送给本机 ELF Loader。必须先运行兼容的 elfldr；否则网页会报告 Loader 连接失败。The web UI scans plugins, payloads, and ELFs, sending start/restart requests to the local ELF Loader. A compatible elfldr must be running, or the web UI will report a connection failure.
配置文件 / Config file: /data/garlic/elf_plugins.json

☁️ 存档云备份 / Save Cloud Backup
支持以下目标：Supports the following targets: * PS5 本地目录 / PS5 Local Directory * USB 目录 / USB Directory * FTP * Google Drive
云配置文件 / Cloud config: /data/garlic/cloud.json
FTP 增量备份 / FTP Incremental Backup
FTP 使用固定的远端文件名，并通过 FTP MDTM 比较远端时间与本地存档源文件的最新修改时间：FTP uses fixed remote filenames and compares remote time with local save modification time via MDTM: * 本地较新 / Local Newer: 重新打包并上传。Repack and upload. * 时间相同 / Time Same: 跳过上传。Skip upload. * 远端更新 / Remote Newer: 跳过上传。Skip upload. * FTP 不支持 MDTM 或远端文件不存在 / No MDTM or File Missing: 正常上传。Upload normally.
自动备份需要：Auto-backup requires: 1. 启用云备份及至少一个备份目标。Enable Cloud Backup and at least one target. 2. FTP 目标需单独勾选启用，仅把提供方选为 FTP 不代表目标已启用。FTP target must be enabled separately; selecting FTP provider does not auto-enable the target. 3. watch_titles 留空表示全部；填写后只有列表中的 Title ID 才自动备份。Empty watch_titles means all; filled list restricts auto-backup to listed Title IDs. 4. 游戏关闭或回到主页后，生命周期监视器等待存档写盘稳定，再检查变化并开始备份。After game close or return to home, the lifecycle monitor waits for save write stability before checking changes and backing up.

📝 状态和日志 / Status & Logs
网页右下角显示真实 PS5 系统版本。首页底部显示内部存储和 ext1 的容量与剩余空间。Bottom right of web UI shows real PS5 system version. Bottom of home page shows internal storage and ext1 capacity/usage.
统一日志目录 / Unified Log Directory: /data/sgfcnlog/
主要日志文件 / Main Log Files:
/data/sgfcnlog/shadowmountplus.log
/data/sgfcnlog/garlic.log
/data/sgfcnlog/ftpsrv.log
/data/sgfcnlog/ghostcontrol.log
/data/sgfcnlog/sync_history.log
/data/sgfcnlog/elf_plugins.log
查看方式 / How to view: * 首页点击“查看近期同步日志”。Click “View Recent Sync Logs” on home page. * 使用 ftpsrv 下载 /data/sgfcnlog/。Download /data/sgfcnlog/ via ftpsrv. * 通过其他 PS5 文件管理工具直接读取日志文件。Read log files directly via other PS5 file managers.

❓ 常见问题 / FAQ
•网页打不开 / Web UI not opening
◦确认启动通知中的网页端口为 7777。Confirm web port is 7777 in startup notification.
◦确认设备与 PS5 在同一局域网。Confirm device and PS5 are on the same LAN.
◦检查路由器客户端隔离和防火墙。Check router client isolation and firewall.
◦若高级设置禁用了 PS5 Console Web，需要修改配置或重新部署恢复。If PS5 Console Web is disabled in Advanced Settings, modify config or redeploy to restore.
•FTP 测试提示目标未启用 / FTP Test: Target not enabled
◦在高级设置中同时启用云备份和 FTP 备份目标。错误 FTP backup target is not enabled 属于配置问题，不是网络连接失败。Enable both Cloud Backup and FTP target in Advanced Settings. Error FTP backup target is not enabled is a config issue, not a network failure.
•自动备份提示不在 watch_titles / Auto-backup: Not in watch_titles
◦把 Title ID 加入监视列表，或清空监视列表以允许所有游戏。Add Title ID to watch list, or clear list to allow all games.
•游戏管理出现重复 / Duplicate Games in Management
◦新版按扫描根目录、完整路径和 Title ID 去重，并让 ShadowMountPlus 来源优先。部署新版 ELF 后刷新页面；若浏览器仍显示旧界面，强制刷新或清除该 PS5 地址的站点缓存。New version deduplicates by root, path, and Title ID, prioritizing ShadowMountPlus. Refresh page after deploying new ELF; if old UI persists, hard refresh or clear site cache.
•扫描目录无法读取 / Scan Directory Unreadable
◦检查目录是否存在、设备是否挂载、路径是否重复，以及 PS5 是否有权限访问。无效目录应从高级设置中删除。Check if directory exists, device is mounted, path is unique, and PS5 has permission. Remove invalid directories from Advanced Settings.
