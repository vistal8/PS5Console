# PS5 Console

<div align="center">

**语言切换 / Language / Langue / 言語 / Язык**

[🇨🇳 中文](#-中文) · [🇺🇸 English](#-english) · [🇫🇷 Français](#-français) · [🇯🇵 日本語](#-日本語) · [🇷🇺 Русский](#-русский)

</div>

---

## 🇨🇳 中文

[⬆️ 返回语言选择](#ps5-console)

### 免责声明

本项目按"原样"提供，不保证适用于所有 PS5 固件、硬盘、镜像或 USB 手柄。作者及上游贡献者不对数据丢失、系统错误、账号风险、硬件损坏或其他后果负责。使用前请备份重要数据，并充分了解相关操作的风险。

### PS5 Console

一体化 PS5 本地控制中心，将游戏挂载、存档管理、USB 手柄兼容、FTP、风扇温控和 ELF 管理整合到一个 Payload 与 Web UI 中。

当前项目用于已具备 Payload 运行环境的 PS5。请仅处理你合法拥有的游戏与存档，并自行承担测试第三方 Payload、修改系统数据库和挂载镜像的风险。

### 项目介绍

PS5 Console 基于多个开源 PS5 项目整合开发，目标是减少多个 Payload 分别加载、配置分散和操作入口不统一的问题。加载一个 ELF 后，即可通过浏览器访问统一控制中心：

```
http://<PS5-IP>:7777/
```

当前集成组件与功能模块：

| 组件 / 功能模块 | 作用 |
| --- | --- |
| 控制中心 | 集中显示游戏扫描、USB 手柄、风扇温控、服务状态和常用操作入口 |
| ShadowMountPlus | 扫描、挂载并注册 PS5 游戏镜像，支持开机扫描与 Web 手动扫描 |
| 游戏管理 | 搜索和查看游戏、挂载来源及注册状态，并提供卸载 PKG、删除挂载源等操作 |
| 挂载检查与修复 | 检查系统已注册游戏及元数据状态，通过受控手动扫描修复失效挂载 |
| 游戏标题净化 | 自动移除游戏标题末尾的域名广告，并同步修正元数据副本和 app.db 标题 |
| Garlic Save Manager | 浏览、解密、加密、重签、导入及备份 PS4/PS5 存档 |
| GhostControl | 将兼容 USB 手柄映射为 PS5 虚拟手柄，并管理扫描、状态、重绑和全部释放 |本人目前主要适配的是Start云游戏手柄 Gamesir T4-Start
| PS4 游戏手柄重连 | 启动 CUSA 游戏后安全重建并绑定 VDA，避免首次进入游戏后手柄无响应 |
| ftpsrv | 提供 PS5 文件访问与传输服务，默认监听 FTP 端口 2121 |
| ps5-fan-control | 设置风扇目标温度并读取 CPU/GPU 温度 |
| 温度历史与高温预警 | 保存最近 60 次 CPU/GPU 温度采样，并在 CPU 达到 80°C 时有限频提醒 |
| ELF 管理器 | 扫描和启动 etaHEN 插件、Payload 与 ELF，并阻止再次启动 PS5 Console 自身 |
| 系统健康检查 | 检测单实例锁、插件文件、GhostControl VDA 错误、重复自身注入、LVD 超时及高温 |
| 游戏启动诊断 | 游戏启动时记录 Title ID、温度、风扇和 GhostControl 状态快照 |
| 一键诊断报告 | 汇总组件状态、日志尾部和游戏启动快照，生成可下载的诊断报告 |
| 配置备份与恢复 | 支持配置备份、恢复、导入和导出，并在导入或恢复前自动保留当前版本 |
| 安全更新与回滚 | 从浏览器上传并校验新版 ELF，安装前备份当前插件，并支持回滚上一版 |
| 任务中心 | 显示扫描、挂载修复、配置、更新和诊断任务的进度与最终结果 |
| 日志管理 | 在 Web 终端查看运行信息，并通过二次确认清空当前日志和轮转日志 |
| 多语言界面 | 在多种语言之间切换，并通过浏览器本地存储保留语言选择 |

### 主要功能

#### 游戏扫描与挂载

- 支持 .ffpkg、.exfat、.ffpfs 和 .ffpfsc 镜像。
- 每次 PS5 完整开机后，Payload 首次启动时自动扫描并挂载一次。
- 同一次开机内重载 Payload、待机唤醒均不会自动重复扫描。
- 其余扫描由 Web UI 的"扫描新游戏"手动触发。
- 支持自定义扫描目录、扫描深度、只读挂载、强制挂载和 fakelib/backport 配置。
- 游戏管理页可查看挂载来源、搜索游戏、卸载 PKG 或删除挂载源。
- 自动净化镜像元数据中附加在游戏名末尾的网站域名，并同步修正 app.db 中已注册的标题。
- 推荐优先使用 UFS .ffpkg。仅在游戏需要外置存储兼容方式时使用 .exfat；手动制作 exFAT 镜像时建议使用 64 KB 簇大小。PFS 支持仍属于实验功能。

#### USB 手柄与 GhostControl

- 开机时自动扫描一次已连接的 USB 手柄。
- 开机后新连接的手柄可通过 Web UI"扫描 USB 手柄"手动识别。
- PS5 游戏启动后自动刷新虚拟手柄与当前用户的绑定，无需重新插拔。
- PS4（CUSA）游戏启动后会等待输入系统初始化，再安全重建并绑定 VDA，模拟完整的手柄重新连接事件。
- 待机前释放 USB endpoint 和虚拟手柄，避免唤醒后占满手柄绑定槽位。
- 停止后台周期性 USB 枚举、高频成功日志和周期实体手柄检查，降低游戏中的 USB 总线、CPU 与磁盘写入负担。
- 单实例锁阻止重复注入创建多个 GhostControl 管理线程。

#### 存档工具

- 浏览 PS4、PS5、本机和 USB 存档。
- 支持存档解密、加密、重签、导入、下载与删除。
- 支持本地目录、USB、FTP 和 Google Drive 队列/配置模式备份。
- FTP 备份可比较修改时间，未发生变化时跳过重复上传。
- 可监视游戏退出后的存档变化并执行自动备份。

#### 控制中心与界面

- 多语言界面切换，语言选择保存在浏览器本地存储中。
- 桌面端主导航居中，移动端使用折叠菜单。
- 风扇温控启用时显示 CPU/GPU 双温度仪表。
- 风扇卡片的"设置"按钮可直接跳转到高级设置中的风扇区域。
- 终端区域可查看运行信息，并通过二次确认清空所有组件日志及轮转日志。
- 支持扫描 /data/etaHEN/plugins 与 /data/etaHEN/payloads 中的外部 ELF。
- 禁止从控制中心再次启动 PS5 Console 自身，避免递归注入。

#### 风扇温控

- 可设置 30–90°C 的目标温度。
- 可配置重应用间隔和 CPU/GPU 温度通知。
- 风扇仍由 PS5 系统驱动管理，本项目只修改目标温度阈值，不提供固定转速或自定义风扇曲线。

### 本次更新与修复

#### 2026-07-13

**PS4 游戏 USB 手柄修复**

- 修复 CUSA 游戏首次进入后 USB 手柄无响应，必须物理拔插并重新扫描的问题。
- 游戏启动约 4.5 秒后暂停对应槽位输入，删除旧 VDA，并在原 USB endpoint 与读取线程上创建、绑定新 VDA。
- VDA 创建失败时自动重试 3 次，不额外扫描 USB，也不长期占用更多虚拟手柄槽位。
- PPSA 游戏继续采用较轻量的原地重新绑定，避免不必要的设备重建。
- GhostControl 增加组件级互斥启动保护，阻止并发初始化多个管理线程。

**Web 安全更新**

- 控制中心可直接选择并上传本地 PS5_Console.elf，无需先通过 FTP 放入暂存目录。
- 上传限制为 4 KB 至 32 MB，并校验 ELF64、Little Endian、AMD64 与 PIE/可执行文件类型。
- 上传先写入 .part 文件，完整接收和校验成功后才原子替换暂存版本；安装时再通过插件目录内的 .new 文件复检并原子替换当前 ELF。
- 安装前自动把当前插件备份为 /data/etaHEN/plugins/PS5_Console.elf.bak，支持一键回滚。
- 更新只替换磁盘文件，不热替换正在执行的代码；安装或回滚后必须安全关闭当前实例，再由 etaHEN 或 ELF Loader 重新加载。

#### 2026-07-12

**USB 手柄与稳定性**

- 修复待机后虚拟手柄未完全释放，导致"无法连接更多蓝牙手柄"的问题。
- 修复 USB 手柄首次进入游戏后无法操控、必须拔插并重新扫描的问题。
- 修复 GhostControl 被重复加载后占用多个 VDA 手柄槽位的问题。
- 增加 /data/PS5Console/PS5_Console.lock 单实例锁；重复注入时新实例直接退出。
- 待机前暂停扫描、关闭 endpoint 并删除虚拟设备。
- 游戏启动后延迟刷新现有 VDA 绑定，不创建额外虚拟手柄。
- USB 自动枚举改为开机一次，其余操作由 Web UI 手动扫描。
- 移除高频 VDI 成功日志、周期实体手柄检查和持续 USB 枚举，改善连接 USB 手柄后的卡顿。

**游戏挂载与标题**

- 修复待机清理挂载后，主界面保留游戏图标但游戏无法启动的问题定位流程。
- 自动扫描规则改为每次完整开机一次；待机、重载 Payload 后不重复扫描。
- 新增 boot_scan.marker，识别同一次系统启动。
- 新增游戏标题净化：删除标题末尾的域名广告，并修正 param.json 副本及 app.db 已注册标题。
- 普通安装和批量安装路径均应用标题净化，不修改原始游戏镜像。

**日志、配置与性能**

- 日志和配置统一迁移到 /data/PS5Console/，首次运行时自动迁移旧文件。
- Web UI 新增"清空日志"，不会删除配置文件。
- 移除风扇线程周期性 [FAN] threshold=... 调试输出。
- 增强待机、唤醒、USB 手柄重绑定和单实例拦截日志。
- 修复旧配置中无效字段导致的重复警告。

**Web UI**

- "Payload 管理管理"更名为"控制中心"。
- 新增多语言切换并补齐动态状态、设置说明和终端信息翻译。
- 新增 CPU/GPU 温度仪表，仅在风扇温控开启时显示。
- 新增"扫描 USB 手柄"按钮及多语言结果反馈。
- 高级设置内容和桌面端顶部主导航改为居中显示。
- 关闭提示署名更正为 By LeLe，移除遗留的外部赞助按钮。
- 风扇"设置"按钮可直接定位到对应高级设置区域。

### 安装与使用

1. 将 PS5_Console.elf 放入常用 Payload Loader 或 etaHEN 插件目录。
2. 确保同一次开机只启用一种自动加载方式，推荐使用 etaHEN 插件自动加载。
3. 加载 ELF，等待 PS5 Console 启动通知。
4. 在同一局域网的浏览器访问 `http://<PS5-IP>:7777/`。
5. 完整开机后的首次加载会自动扫描游戏与已连接的 USB 手柄；之后按需使用 Web UI 手动扫描。

etaHEN 插件部署路径示例：

```
/data/etaHEN/plugins/PS5_Console.elf
```

替换新版 ELF 时，先退出游戏并停止正在进行的扫描、挂载和存档写入，再关闭旧实例并重新加载。已经发生虚拟手柄槽位残留时，应完整关机并断电片刻，以清除旧版本留下的内核资源。

### 运行目录

新版将自身日志和配置集中保存到：

```
/data/PS5Console/
```

常见文件包括：

```
/data/PS5Console/config.ini
/data/PS5Console/shadowmountplus.log
/data/PS5Console/ghostcontrol.log
/data/PS5Console/garlic.log
/data/PS5Console/ftpsrv.log
/data/PS5Console/elf_plugins.log
/data/PS5Console/sync_history.log
/data/PS5Console/boot_scan.marker
/data/PS5Console/PS5_Console.lock
```

部分集成组件可能保留各自的兼容配置文件；Web UI 会显示实际生效路径。升级后如需诊断，请先通过界面清空历史日志，再按"开机 → 扫描 → 启动游戏 → 复现问题"的顺序测试并导出新日志。

### 2026-07-13 系统工具更新

- 新增系统健康检查，集中显示单实例锁、插件文件、GhostControl VDA 错误、自身注入拦截、运行游戏与温度状态。
- 新增游戏启动诊断快照，自动写入 /data/PS5Console/game_diagnostics.log。
- 新增一键诊断报告，下载各组件日志尾部与游戏启动快照，便于复现后提交问题。
- 新增 ShadowMountPlus 挂载状态检查与受控修复扫描。
- 新增配置备份、恢复、导入和导出；导入或恢复前自动保留当前配置。
- 新增安全更新与回滚。暂存 ELF 路径为 /data/PS5Console/update/PS5_Console.elf，安装前校验 ELF，当前版本备份为 /data/etaHEN/plugins/PS5_Console.elf.bak。
- 新增 CPU/GPU 温度历史，浏览器保存最近 60 个采样点；CPU 达到 80°C 时进行有限频高温提醒。
- 新增任务中心，记录游戏扫描、USB 手柄扫描、挂载修复、配置操作、安全更新及游戏启动诊断。

> 安全更新不会热替换正在执行的 Payload 代码。文件切换完成后，应安全关闭当前 PS5 Console，再由 etaHEN 或 ELF Loader 重新加载新版。

### Web 更新与回滚

1. 打开"控制中心 → 系统健康与诊断 → 安全更新与回滚"。
2. 点击"上传新版 ELF"，选择本地的 PS5_Console.elf。
3. 等待界面显示"暂存版本 · 已校验"。
4. 退出游戏并确认没有扫描、挂载、存档或 FTP 写入任务。
5. 点击"安装暂存版本"，当前插件会先自动备份。
6. 安全关闭当前 PS5 Console，再通过 etaHEN 或 ELF Loader 重新加载。

如果新版无法正常运行，可重新加载仍能访问 Web UI 的版本并点击"回滚上一版"。也可以直接恢复：

```
/data/etaHEN/plugins/PS5_Console.elf.bak
```

上传接口只接受本地网络中的原始 ELF 文件，不会从互联网下载更新，也不会自动执行未经确认的文件。

### 兼容性与注意事项

- 需要可运行 Payload 的越狱 PS5 环境，以及与固件匹配的 etaHEN/kstuff。
- etaHEN 已集成 kstuff 时，不要再单独加载另一份 kstuff-lite；重复内核补丁可能造成卡顿、冻结或系统错误。
- 不要同时启用 etaHEN 自动加载、其他插件自动加载和手动注入 PS5 Console。
- 不要同时运行独立 BackPork 与本项目的 fakelib/backport 功能。
- 挂载镜像、修改系统数据库及测试实验性 PFS 可能导致关机异常或数据损坏。
- 删除挂载源不可恢复；卸载、重签、导入或覆盖存档前请先备份。
- 游戏或存档正在读写时，不要拔出 USB 设备、断电或强制结束 Payload。
- 若游戏卡顿，请先排除过热、USB 存储性能、叠加层/作弊插件以及重复 kstuff，而不是直接认定为挂载失败。

### 常见问题

**游戏图标存在，但启动失败**

待机或关机流程可能已经清理镜像挂载。确认 USB 存储已识别，然后在控制中心点击"扫描新游戏"，等待挂载和注册完成。

**USB 手柄进入游戏后没有反应**

新版会在游戏启动后自动刷新绑定。若仍无反应，请在控制中心点击"扫描 USB 手柄"，并检查 ghostcontrol.log 中是否有 VDA 创建或绑定错误。首次从旧版升级且系统仍提示无法连接更多手柄时，需要完整关机清理旧资源。

**连接 USB 手柄后游戏卡顿**

确认使用的是包含 GhostControl 热路径优化的新版 ELF，并清空旧日志后重新测试。同时关闭 etaHEN Controller Shortcuts、Overlay、FPS/温度叠加层和不必要的作弊插件进行对照。

**切换语言后仍显示原语言**

刷新页面并清除该 PS5 地址的旧站点缓存。动态界面由新版翻译表处理，浏览器缓存旧 Web UI 时可能继续显示旧文案。

**网页打不开**

确认 Payload 已运行、设备与 PS5 位于同一局域网，并访问端口 7777。同时检查路由器客户端隔离和防火墙设置。

### 致谢与上游项目

PS5 Console 建立在以下项目及 PS5 开源社区工作的基础上：


- [ShadowMountPlus](https://github.com/drakmor/shadowMountPlus)
- [Garlic Save Manager](https://github.com/earthonion/garlic)
- GhostControl
- ps5-fan-control
- ftpsrv
- [etaHEN](https://github.com/etaHEN/etaHEN)
- [kstuff-lite](https://github.com/EchoStretch/kstuff-lite)
- [PS5 Payload SDK](https://github.com/ps5-payload-dev/sdk

各组件继续遵循其原始许可证与署名要求。合并源码、上游许可证和原始说明应随发布内容一并保留。

---
### 界面预览
<img width="2560" height="1271" alt="PS5 Console" src="https://github.com/user-attachments/assets/efd65a7c-03be-4926-8bca-74db8bfc69db" />
<img width="2560" height="1271" alt="PS5 Consoleadv" src="https://github.com/user-attachments/assets/440ca3a8-5413-40cb-9da5-421f7ee41350" />
<img width="2560" height="1271" alt="PS5 Console" src="https://github.com/user-attachments/assets/3eb0b414-7d46-4927-bdaa-4b6e87d90250" />



## 🇺🇸 English

[⬆️ Back to language selection](#ps5-console)

### Disclaimer

This project is provided "as-is" with no guarantee of compatibility with all PS5 firmwares, hard drives, game images, or USB controllers. The author and upstream contributors are not responsible for data loss, system errors, account risks, hardware damage, or any other consequences. Please back up important data and fully understand the risks of related operations before use.

### PS5 Console

An all-in-one local control center for PS5 that integrates game mounting, save management, USB controller compatibility, FTP, fan temperature control, and ELF management into a single Payload with Web UI.

This project is intended for PS5 systems already capable of running Payloads. Please only use it with games and saves that you legally own, and assume full responsibility for testing third-party Payloads, modifying system databases, and mounting game images.

### Project Introduction

PS5 Console is built by integrating multiple open-source PS5 projects, aiming to solve the problems of loading multiple Payloads separately, scattered configurations, and inconsistent operation entry points. After loading one ELF, you can access a unified control center through your browser:

```
http://<PS5-IP>:7777/
```

Currently integrated components and feature modules:

| Component / Module | Function |
| --- | --- |
| Control Center | Centralized display of game scanning, USB controllers, fan control, service status, and common operations |
| ShadowMountPlus | Scan, mount, and register PS5 game images; supports boot scan and manual Web scan |
| Game Management | Search and view games, mount sources, and registration status; provides uninstall PKG, delete mount source operations |
| Mount Check & Repair | Check system-registered games and metadata status; fix broken mounts via controlled manual scans |
| Game Title Sanitization | Automatically removes domain ads from game title endings and syncs fixes to metadata copies and app.db titles |
| Garlic Save Manager | Browse, decrypt, encrypt, re-sign, import, and back up PS4/PS5 saves |
| GhostControl | Maps compatible USB controllers to PS5 virtual controllers; manages scanning, status, rebinding, and full release |
| PS4 Game Controller Reconnect | Safely rebuilds and binds VDA after launching CUSA games to prevent unresponsive controllers on first launch |
| ftpsrv | PS5 file access and transfer service; default FTP port 2121 |
| ps5-fan-control | Set fan target temperature and read CPU/GPU temperatures |
| Temperature History & High-Temp Alert | Saves last 60 CPU/GPU temperature samples; throttling alert when CPU reaches 80°C |
| ELF Manager | Scan and launch etaHEN plugins, Payloads, and ELFs; prevents relaunching PS5 Console itself |
| System Health Check | Detects single-instance lock, plugin files, GhostControl VDA errors, duplicate self-injection, LVD timeout, and high temperature |
| Game Launch Diagnostics | Records Title ID, temperature, fan, and GhostControl status snapshots on game launch |
| One-Click Diagnostic Report | Aggregates component status, log tails, and game launch snapshots into a downloadable diagnostic report |
| Config Backup & Restore | Supports config backup, restore, import, and export; auto-preserves current version before import or restore |
| Safe Update & Rollback | Upload and verify new ELF versions from browser; backs up current plugin before installation and supports rollback |
| Task Center | Shows progress and final results for scan, mount repair, config, update, and diagnostic tasks |
| Log Management | View runtime info in Web terminal; clear current and rotated logs with double confirmation |
| Multilingual UI | Switch between multiple languages; preference saved in browser local storage |

### Main Features

#### Game Scanning & Mounting

- Supports .ffpkg, .exfat, .ffpfs, and .ffpfsc images.
- Automatically scans and mounts once on first Payload launch after a full PS5 boot.
- Reloading Payload or waking from rest mode within the same boot cycle does not trigger repeated auto-scans.
- Additional scans are triggered manually via "Scan New Games" in the Web UI.
- Supports custom scan directories, scan depth, read-only mounting, forced mounting, and fakelib/backport configuration.
- Game management page shows mount sources, game search, PKG uninstall, and mount source deletion.
- Automatically sanitizes website domains appended to game names in image metadata and syncs fixes to registered titles in app.db.
- UFS .ffpkg is recommended first. Use .exfat only when external storage compatibility is required; 64 KB cluster size is recommended for manually created exFAT images. PFS support remains experimental.

#### USB Controllers & GhostControl

- Automatically scans connected USB controllers once on boot.
- Newly connected controllers after boot can be manually identified via "Scan USB Controllers" in Web UI.
- Automatically refreshes virtual controller binding to the current user after PS5 game launch — no re-plug needed.
- After PS4 (CUSA) game launch, waits for input system initialization, then safely rebuilds and binds VDA to simulate a full controller reconnect event.
- Releases USB endpoints and virtual controllers before entering rest mode to prevent filling controller binding slots on wake.
- Eliminates background periodic USB enumeration, high-frequency success logs, and periodic physical controller checks to reduce USB bus, CPU, and disk write overhead during gameplay.
- Single-instance lock prevents duplicate injection from creating multiple GhostControl manager threads.

#### Save Tools

- Browse PS4, PS5, local, and USB saves.
- Supports save decryption, encryption, re-signing, import, download, and deletion.
- Supports backup via local directory, USB, FTP, and Google Drive queue/config modes.
- FTP backup compares modification times and skips re-uploading unchanged files.
- Can monitor save changes after game exit and perform automatic backups.

#### Control Center & UI

- Multilingual UI switching; language preference saved in browser local storage.
- Desktop main navigation centered; mobile uses collapsible menu.
- Dual CPU/GPU temperature gauges shown when fan control is enabled.
- Fan card "Settings" button jumps directly to the fan section in advanced settings.
- Terminal area shows runtime info; clear all component and rotated logs with double confirmation.
- Supports scanning external ELFs in /data/etaHEN/plugins and /data/etaHEN/payloads.
- Prevents relaunching PS5 Console itself from the control center to avoid recursive injection.

#### Fan Temperature Control

- Target temperature can be set between 30–90°C.
- Configurable re-apply interval and CPU/GPU temperature notifications.
- Fan speed remains managed by the PS5 system driver; this project only modifies the target temperature threshold and does not provide fixed RPM or custom fan curves.

### Recent Updates & Fixes

#### 2026-07-13

**PS4 Game USB Controller Fix**

- Fixed USB controllers becoming unresponsive after first launch of CUSA games, which previously required physical re-plug and rescan.
- Approximately 4.5 seconds after game launch, input on the corresponding slot is paused, old VDA is deleted, and a new VDA is created and bound on the original USB endpoint and read thread.
- VDA creation failures auto-retry 3 times without extra USB scanning or long-term occupation of additional virtual controller slots.
- PPSA games continue using lighter in-place rebinding to avoid unnecessary device reconstruction.
- GhostControl adds component-level mutual exclusion startup protection to prevent concurrent initialization of multiple manager threads.

**Web Safe Update**

- Control center can directly select and upload local PS5_Console.elf — no need to stage via FTP first.
- Upload size limited to 4 KB – 32 MB; validates ELF64, Little Endian, AMD64, and PIE/executable file types.
- Uploads are first written to .part files; atomic replacement of staged version only after full receipt and successful verification. Installation performs secondary verification via .new file in plugin directory before atomically replacing the current ELF.
- Current plugin is automatically backed up to /data/etaHEN/plugins/PS5_Console.elf.bak before installation; one-click rollback supported.
- Updates only replace disk files and do not hot-swap executing code. After install or rollback, you must safely shut down the current instance and reload via etaHEN or ELF Loader.

#### 2026-07-12

**USB Controllers & Stability**

- Fixed virtual controllers not fully released after rest mode, causing "cannot connect more Bluetooth controllers" error.
- Fixed USB controllers becoming unresponsive after first game entry, requiring re-plug and rescan.
- Fixed GhostControl occupying multiple VDA controller slots after duplicate loading.
- Added /data/PS5Console/PS5_Console.lock single-instance lock; duplicate injection causes new instance to exit immediately.
- Pauses scanning, closes endpoints, and deletes virtual devices before rest mode.
- Delays refreshing existing VDA bindings after game launch; no extra virtual controllers created.
- USB auto-enumeration changed to once-on-boot; all other operations use manual Web UI scanning.
- Removed high-frequency VDI success logs, periodic physical controller checks, and continuous USB enumeration to reduce stutter with USB controllers connected.

**Game Mounting & Titles**

- Improved troubleshooting flow for games showing icons on home screen but failing to launch after rest mode clears mounts.
- Auto-scan rule changed to once per full boot; no repeated scanning after rest mode or Payload reload.
- Added boot_scan.marker to identify the same system boot session.
- Added game title sanitization: removes trailing domain ads from titles and fixes param.json copies and registered titles in app.db.
- Title sanitization applied to both normal and batch install paths; original game images are not modified.

**Logs, Config & Performance**

- Logs and config unified under /data/PS5Console/; old files auto-migrated on first run.
- Web UI added "Clear Logs" — does not delete config files.
- Removed periodic [FAN] threshold=... debug output from fan thread.
- Enhanced logging for rest mode, wake, USB controller rebinding, and single-instance interception.
- Fixed duplicate warnings caused by invalid fields in old configs.

**Web UI**

- "Payload Management" renamed to "Control Center".
- Added multilingual switching with full translations for dynamic states, setting descriptions, and terminal messages.
- Added CPU/GPU temperature gauges (shown only when fan control is enabled).
- Added "Scan USB Controllers" button with multilingual result feedback.
- Advanced settings content and desktop top navigation now centered.
- Close prompt attribution corrected to By LeLe; removed legacy external sponsor button.
- Fan "Settings" button jumps directly to the corresponding advanced settings section.

### Installation & Usage

1. Place PS5_Console.elf in your preferred Payload Loader or etaHEN plugins directory.
2. Ensure only one auto-load method is enabled per boot cycle. etaHEN plugin auto-load is recommended.
3. Load the ELF and wait for the PS5 Console startup notification.
4. Access `http://<PS5-IP>:7777/` from a browser on the same local network.
5. First load after a full boot will auto-scan games and connected USB controllers; use Web UI manual scans afterwards as needed.

Example etaHEN plugin deployment path:

```
/data/etaHEN/plugins/PS5_Console.elf
```

When replacing with a new ELF version, exit games and stop ongoing scans, mounts, and save writes first, then shut down the old instance and reload. If virtual controller slots remain from a previous version, perform a full shutdown and power off briefly to clear kernel resources left by the old version.

### Runtime Directory

New version consolidates its logs and configuration under:

```
/data/PS5Console/
```

Common files include:

```
/data/PS5Console/config.ini
/data/PS5Console/shadowmountplus.log
/data/PS5Console/ghostcontrol.log
/data/PS5Console/garlic.log
/data/PS5Console/ftpsrv.log
/data/PS5Console/elf_plugins.log
/data/PS5Console/sync_history.log
/data/PS5Console/boot_scan.marker
/data/PS5Console/PS5_Console.lock
```

Some integrated components may retain their own compatibility config files; the Web UI shows the actual active paths. For post-upgrade diagnostics, first clear historical logs via the UI, then test and export new logs following the sequence: boot → scan → launch game → reproduce issue.

### 2026-07-13 System Tools Update

- Added System Health Check: centralized display of single-instance lock, plugin files, GhostControl VDA errors, self-injection interception, running games, and temperature status.
- Added game launch diagnostic snapshots, automatically written to /data/PS5Console/game_diagnostics.log.
- Added one-click diagnostic report: downloads tail of each component log and game launch snapshots for easy issue submission after reproduction.
- Added ShadowMountPlus mount status check and controlled repair scanning.
- Added config backup, restore, import, and export; current config is auto-preserved before import or restore.
- Added safe update and rollback. Staged ELF path: /data/PS5Console/update/PS5_Console.elf. ELF verified before installation; current version backed up to /data/etaHEN/plugins/PS5_Console.elf.bak.
- Added CPU/GPU temperature history; browser saves last 60 samples; throttling high-temperature alert when CPU reaches 80°C.
- Added Task Center: tracks game scanning, USB controller scanning, mount repair, config operations, safe updates, and game launch diagnostics.

> Safe updates do not hot-swap executing Payload code. After file switching completes, safely shut down the current PS5 Console and reload the new version via etaHEN or ELF Loader.

### Web Update & Rollback

1. Open "Control Center → System Health & Diagnostics → Safe Update & Rollback".
2. Click "Upload New ELF" and select your local PS5_Console.elf.
3. Wait for the UI to show "Staged Version · Verified".
4. Exit games and confirm no scan, mount, save, or FTP write tasks are running.
5. Click "Install Staged Version" — current plugin is auto-backed up first.
6. Safely shut down the current PS5 Console, then reload via etaHEN or ELF Loader.

If the new version fails to run properly, reload a version that still accesses the Web UI and click "Rollback to Previous". You can also manually restore:

```
/data/etaHEN/plugins/PS5_Console.elf.bak
```

The upload endpoint only accepts raw ELF files from the local network. It does not download updates from the internet and will not automatically execute unconfirmed files.

### Compatibility & Notes

- Requires a jailbroken PS5 environment capable of running Payloads, plus etaHEN/kstuff matching your firmware.
- If etaHEN already includes kstuff, do not load a separate kstuff-lite; duplicate kernel patches may cause stutter, freezes, or system errors.
- Do not simultaneously enable etaHEN auto-load, other plugin auto-loaders, and manual injection of PS5 Console.
- Do not run standalone BackPort alongside this project's fakelib/backport feature.
- Mounting images, modifying system databases, and testing experimental PFS may cause shutdown anomalies or data corruption.
- Deleting mount sources is irreversible; back up before uninstalling, re-signing, importing, or overwriting saves.
- Do not unplug USB devices, cut power, or force-kill the Payload while games or saves are being read/written.
- If games stutter, first rule out overheating, USB storage performance, overlays/cheat plugins, and duplicate kstuff before assuming a mount failure.

### FAQ

**Game icon exists but launch fails**

Rest mode or shutdown may have cleared the image mount. Confirm USB storage is recognized, then click "Scan New Games" in the control center and wait for mounting and registration to complete.

**USB controller has no response after entering game**

The new version automatically refreshes bindings after game launch. If still unresponsive, click "Scan USB Controllers" in the control center and check ghostcontrol.log for VDA creation or binding errors. If upgrading from an old version and the system still reports cannot connect more controllers, perform a full shutdown to clean up old resources.

**Game stutters with USB controller connected**

Confirm you are using the new ELF with GhostControl hot-path optimization, clear old logs, and retest. Also disable etaHEN Controller Shortcuts, Overlay, FPS/temperature overlays, and unnecessary cheat plugins for comparison.

**Language still shows original after switching**

Refresh the page and clear old site cache for this PS5 address. The dynamic UI is handled by the new translation table; browser-cached old Web UI may continue showing old text.

**Web page won't open**

Confirm the Payload is running, your device is on the same LAN as the PS5, and you're accessing port 7777. Also check router client isolation and firewall settings.

### Credits & Upstream Projects

PS5 Console is built upon the following projects and the work of the PS5 open-source community:


- [ShadowMountPlus](https://github.com/drakmor/shadowMountPlus)
- [Garlic Save Manager](https://github.com/earthonion/garlic)
- GhostControl
- ps5-fan-control
- ftpsrv
- [etaHEN](https://github.com/etaHEN/etaHEN)
- [kstuff-lite](https://github.com/EchoStretch/kstuff-lite)
- [PS5 Payload SDK](https://github.com/ps5-payload-dev/sdk

Each component continues to follow its original license and attribution requirements. Merged source code, upstream licenses, and original notices shall be retained with releases.

---

## 🇫🇷 Français

[⬆️ Retour au choix de la langue](#ps5-console)

### Avertissement

Ce projet est fourni "tel quel" sans garantie de compatibilité avec tous les firmwares PS5, disques durs, images de jeux ou manettes USB. L'auteur et les contributeurs amont ne sont pas responsables des pertes de données, erreurs système, risques de compte, dommages matériels ou toute autre conséquence. Veuillez sauvegarder vos données importantes et comprendre pleinement les risques des opérations associées avant utilisation.

### PS5 Console

Un centre de contrôle local tout-en-un pour PS5 qui intègre le montage de jeux, la gestion des sauvegardes, la compatibilité des manettes USB, le FTP, le contrôle thermique du ventilateur et la gestion ELF dans un seul Payload avec interface Web.

Ce projet est destiné aux systèmes PS5 déjà capables d'exécuter des Payloads. Veuillez ne l'utiliser qu'avec des jeux et sauvegardes que vous possédez légalement, et assumez l'entière responsabilité du test de Payloads tiers, de la modification des bases de données système et du montage d'images de jeux.

### Présentation du projet

PS5 Console est construit en intégrant plusieurs projets PS5 open-source, dans le but de résoudre les problèmes de chargement séparé de plusieurs Payloads, de configurations dispersées et de points d'entrée d'opération incohérents. Après avoir chargé un ELF, vous pouvez accéder à un centre de contrôle unifié via votre navigateur :

```
http://<PS5-IP>:7777/
```

Composants et modules fonctionnels actuellement intégrés :

| Composant / Module | Fonction |
| --- | --- |
| Centre de contrôle | Affichage centralisé du scan des jeux, manettes USB, contrôle du ventilateur, état des services et opérations courantes |
| ShadowMountPlus | Scanner, monter et enregistrer des images de jeux PS5 ; prend en charge le scan au démarrage et le scan manuel Web |
| Gestion des jeux | Rechercher et consulter les jeux, sources de montage et état d'enregistrement ; propose désinstallation PKG, suppression source de montage |
| Vérification & Réparation de montage | Vérifier les jeux enregistrés et l'état des métadonnées ; réparer les montages défectueux via scans manuels contrôlés |
| Nettoyage titres de jeux | Supprime automatiquement les publicités de domaine à la fin des titres et synchronise les corrections dans les copies de métadonnées et les titres app.db |
| Garlic Save Manager | Parcourir, déchiffrer, chiffrer, resigner, importer et sauvegarder les sauvegardes PS4/PS5 |
| GhostControl | Mappe les manettes USB compatibles vers des manettes virtuelles PS5 ; gère le scan, l'état, la réaffectation et la libération totale |
| Reconnexion manette jeu PS4 | Reconstruit et lie en toute sécurité le VDA après lancement de jeux CUSA pour éviter les manettes non réactives au premier lancement |
| ftpsrv | Service d'accès et de transfert de fichiers PS5 ; port FTP par défaut 2121 |
| ps5-fan-control | Définir la température cible du ventilateur et lire les températures CPU/GPU |
| Historique température & Alerte haute température | Sauvegarde les 60 derniers échantillons de température CPU/GPU ; alerte de limitation quand CPU atteint 80°C |
| Gestionnaire ELF | Scanner et lancer les plugins etaHEN, Payloads et ELFs ; empêche de relancer PS5 Console lui-même |
| Vérification santé système | Détecte le verrouillage instance unique, fichiers plugins, erreurs VDA GhostControl, auto-injection dupliquée, timeout LVD et haute température |
| Diagnostic lancement jeu | Enregistre des instantanés Title ID, température, ventilateur et état GhostControl au lancement du jeu |
| Rapport diagnostic en un clic | Agrège l'état des composants, fins de logs et instantanés de lancement dans un rapport diagnostic téléchargeable |
| Sauvegarde & Restauration config | Prend en charge sauvegarde, restauration, import et export de config ; conserve automatiquement la version actuelle avant import ou restauration |
| Mise à jour sécurisée & Retour arrière | Téléverser et vérifier les nouvelles versions ELF depuis le navigateur ; sauvegarde le plugin actuel avant installation et prend en charge le retour arrière |
| Centre des tâches | Affiche la progression et les résultats finaux des tâches de scan, réparation de montage, config, mise à jour et diagnostic |
| Gestion des logs | Consulter les infos d'exécution dans le terminal Web ; vider les logs courants et tournants avec double confirmation |
| Interface multilingue | Basculer entre plusieurs langues ; préférence sauvegardée dans le stockage local du navigateur |

### Fonctionnalités principales

#### Scan et montage de jeux

- Prend en charge les images .ffpkg, .exfat, .ffpfs et .ffpfsc.
- Scan et monte automatiquement une fois au premier lancement du Payload après un démarrage complet de la PS5.
- Recharger le Payload ou sortir de veille dans le même cycle de démarrage ne déclenche pas de nouveaux scans automatiques.
- Les scans supplémentaires sont déclenchés manuellement via "Scanner de nouveaux jeux" dans l'interface Web.
- Prend en charge les répertoires de scan personnalisés, profondeur de scan, montage en lecture seule, montage forcé et configuration fakelib/backport.
- La page de gestion des jeux affiche les sources de montage, recherche de jeux, désinstallation PKG et suppression de source de montage.
- Nettoie automatiquement les noms de domaine ajoutés aux noms de jeux dans les métadonnées d'image et synchronise les corrections dans les titres enregistrés d'app.db.
- UFS .ffpkg est recommandé en priorité. Utilisez .exfat uniquement quand la compatibilité de stockage externe est requise ; une taille de cluster de 64 Ko est recommandée pour les images exFAT créées manuellement. La prise en charge PFS reste expérimentale.

#### Manettes USB & GhostControl

- Scanne automatiquement les manettes USB connectées une fois au démarrage.
- Les manettes nouvellement connectées après démarrage peuvent être identifiées manuellement via "Scanner les manettes USB" dans l'interface Web.
- Actualise automatiquement la liaison de la manette virtuelle à l'utilisateur courant après lancement d'un jeu PS5 — pas besoin de rebrancher.
- Après lancement d'un jeu PS4 (CUSA), attend l'initialisation du système d'entrée, puis reconstruit et lie en toute sécurité le VDA pour simuler un événement complet de reconnexion de manette.
- Libère les endpoints USB et les manettes virtuelles avant mise en veille pour éviter de remplir les emplacements de liaison de manette au réveil.
- Élimine l'énumération USB périodique en arrière-plan, les logs de succès à haute fréquence et les vérifications périodiques de manette physique pour réduire la charge du bus USB, du CPU et des écritures disque pendant le jeu.
- Le verrouillage d'instance unique empêche l'injection dupliquée de créer plusieurs threads de gestion GhostControl.

#### Outils de sauvegarde

- Parcourir les sauvegardes PS4, PS5, locales et USB.
- Prend en charge déchiffrement, chiffrement, resignature, import, téléchargement et suppression de sauvegardes.
- Prend en charge la sauvegarde via répertoire local, USB, FTP et modes file d'attente/config Google Drive.
- La sauvegarde FTP compare les dates de modification et évite de télécharger à nouveau les fichiers inchangés.
- Peut surveiller les changements de sauvegarde après fermeture du jeu et effectuer des sauvegardes automatiques.

#### Centre de contrôle & Interface

- Changement d'interface multilingue ; préférence de langue sauvegardée dans le stockage local du navigateur.
- Navigation principale de bureau centrée ; mobile utilise menu repliable.
- Deux jauges de température CPU/GPU affichées quand le contrôle du ventilateur est activé.
- Le bouton "Paramètres" de la carte ventilateur accède directement à la section ventilateur des paramètres avancés.
- Zone terminal affiche les infos d'exécution ; vider tous les logs de composants et tournants avec double confirmation.
- Prend en charge le scan d'ELF externes dans /data/etaHEN/plugins et /data/etaHEN/payloads.
- Empêche de relancer PS5 Console lui-même depuis le centre de contrôle pour éviter l'injection récursive.

#### Contrôle thermique du ventilateur

- La température cible peut être définie entre 30–90°C.
- Intervalle de réapplication et notifications de température CPU/GPU configurables.
- La vitesse du ventilateur reste gérée par le pilote système PS5 ; ce projet ne modifie que le seuil de température cible et ne propose pas de RPM fixe ni de courbes de ventilateur personnalisées.

### Mises à jour et correctifs récents

#### 2026-07-13

**Correctif manette USB pour jeux PS4**

- Correction des manettes USB devenant non réactives après le premier lancement de jeux CUSA, qui nécessitait auparavant un rebranchement physique et un rescan.
- Environ 4,5 secondes après le lancement du jeu, l'entrée sur l'emplacement correspondant est mise en pause, l'ancien VDA est supprimé, et un nouveau VDA est créé et lié sur l'endpoint USB d'origine et le thread de lecture.
- Les échecs de création VDA se réessayent automatiquement 3 fois sans scan USB supplémentaire ni occupation à long terme d'emplacements de manette virtuelle supplémentaires.
- Les jeux PPSA continuent d'utiliser une réaffectation sur place plus légère pour éviter toute reconstruction d'appareil inutile.
- GhostControl ajoute une protection de démarrage à exclusion mutuelle au niveau composant pour empêcher l'initialisation simultanée de plusieurs threads de gestion.

**Mise à jour Web sécurisée**

- Le centre de contrôle peut directement sélectionner et téléverser PS5_Console.elf local — pas besoin de passer par FTP d'abord.
- Taille de téléversement limitée à 4 Ko – 32 Mo ; valide les types ELF64, Little Endian, AMD64 et PIE/exécutable.
- Les téléversements sont d'abord écrits dans des fichiers .part ; remplacement atomique de la version temporaire seulement après réception complète et vérification réussie. L'installation effectue une vérification secondaire via un fichier .new dans le répertoire des plugins avant de remplacer atomiquement l'ELF actuel.
- Le plugin actuel est automatiquement sauvegardé dans /data/etaHEN/plugins/PS5_Console.elf.bak avant installation ; retour arrière en un clic pris en charge.
- Les mises à jour ne remplacent que les fichiers disque et ne remplacent pas à chaud le code en cours d'exécution. Après installation ou retour arrière, vous devez arrêter proprement l'instance actuelle et recharger via etaHEN ou ELF Loader.

#### 2026-07-12

**Manettes USB & Stabilité**

- Correction des manettes virtuelles non totalement libérées après mise en veille, provoquant l'erreur "impossible de connecter plus de manettes Bluetooth".
- Correction des manettes USB devenant inutilisables après première entrée en jeu, nécessitant rebranchement et rescan.
- Correction de GhostControl occupant plusieurs emplacements de manette VDA après chargement dupliqué.
- Ajout du verrouillage instance unique /data/PS5Console/PS5_Console.lock ; l'injection dupliquée provoque la sortie immédiate de la nouvelle instance.
- Met en pause le scan, ferme les endpoints et supprime les appareils virtuels avant mise en veille.
- Retarde l'actualisation des liaisons VDA existantes après lancement du jeu ; aucune manette virtuelle supplémentaire créée.
- Énumération automatique USB changée à une fois au démarrage ; toutes les autres opérations utilisent le scan manuel de l'interface Web.
- Suppression des logs de succès VDI à haute fréquence, vérifications périodiques de manette physique et énumération USB continue pour réduire les saccades avec manettes USB connectées.

**Montage de jeux & Titres**

- Amélioration du flux de dépannage pour les jeux affichant une icône sur l'écran d'accueil mais échouant au lancement après que la veille a nettoyé les montages.
- Règle de scan automatique changée à une fois par démarrage complet ; pas de scan répété après mise en veille ou rechargement du Payload.
- Ajout de boot_scan.marker pour identifier la même session de démarrage système.
- Ajout du nettoyage de titres de jeux : supprime les publicités de domaine en fin de titre et corrige les copies param.json et les titres enregistrés dans app.db.
- Le nettoyage de titres s'applique aux chemins d'installation normale et groupée ; les images de jeux originales ne sont pas modifiées.

**Logs, Config & Performances**

- Logs et config unifiés sous /data/PS5Console/ ; anciens fichiers migrés automatiquement au premier lancement.
- Interface Web ajoute "Vider les logs" — ne supprime pas les fichiers de config.
- Suppression de la sortie de débogage périodique [FAN] threshold=... du thread ventilateur.
- Journalisation renforcée pour la mise en veille, le réveil, la réaffectation de manette USB et l'interception d'instance unique.
- Correction des avertissements dupliqués causés par des champs invalides dans d'anciennes configs.

**Interface Web**

- "Gestion Payload" renommé en "Centre de contrôle".
- Ajout du changement multilingue avec traductions complètes des états dynamiques, descriptions de paramètres et messages du terminal.
- Ajout de jauges de température CPU/GPU (affichées uniquement quand le contrôle du ventilateur est activé).
- Ajout du bouton "Scanner les manettes USB" avec retour de résultat multilingue.
- Contenu des paramètres avancés et navigation supérieure de bureau désormais centrés.
- Attribution du message de fermeture corrigée en By LeLe ; suppression de l'ancien bouton de sponsor externe.
- Le bouton "Paramètres" du ventilateur accède directement à la section des paramètres avancés correspondante.

### Installation & Utilisation

1. Placez PS5_Console.elf dans votre répertoire Payload Loader préféré ou le répertoire des plugins etaHEN.
2. Assurez-vous qu'une seule méthode de chargement automatique est activée par cycle de démarrage. Le chargement automatique du plugin etaHEN est recommandé.
3. Chargez l'ELF et attendez la notification de démarrage de PS5 Console.
4. Accédez à `http://<PS5-IP>:7777/` depuis un navigateur sur le même réseau local.
5. Le premier chargement après un démarrage complet scanne automatiquement les jeux et les manettes USB connectées ; utilisez ensuite les scans manuels de l'interface Web selon vos besoins.

Exemple de chemin de déploiement de plugin etaHEN :

```
/data/etaHEN/plugins/PS5_Console.elf
```

Lors du remplacement par une nouvelle version ELF, quittez les jeux et arrêtez les scans, montages et écritures de sauvegarde en cours, puis arrêtez l'ancienne instance et rechargez. Si des emplacements de manette virtuelle persistent d'une version précédente, effectuez un arrêt complet et coupez l'alimentation brièvement pour nettoyer les ressources noyau laissées par l'ancienne version.

### Répertoire d'exécution

La nouvelle version regroupe ses logs et sa configuration sous :

```
/data/PS5Console/
```

Fichiers courants inclus :

```
/data/PS5Console/config.ini
/data/PS5Console/shadowmountplus.log
/data/PS5Console/ghostcontrol.log
/data/PS5Console/garlic.log
/data/PS5Console/ftpsrv.log
/data/PS5Console/elf_plugins.log
/data/PS5Console/sync_history.log
/data/PS5Console/boot_scan.marker
/data/PS5Console/PS5_Console.lock
```

Certains composants intégrés peuvent conserver leurs propres fichiers de config de compatibilité ; l'interface Web affiche les chemins actifs réels. Pour un diagnostic après mise à jour, commencez par vider les logs historiques via l'interface, puis testez et exportez de nouveaux logs en suivant l'ordre : démarrage → scan → lancer le jeu → reproduire le problème.

### Mise à jour des outils système 2026-07-13

- Ajout de la vérification santé système : affichage centralisé du verrouillage instance unique, fichiers plugins, erreurs VDA GhostControl, interception d'auto-injection, jeux en cours d'exécution et état de température.
- Ajout d'instantanés de diagnostic de lancement de jeu, écrits automatiquement dans /data/PS5Console/game_diagnostics.log.
- Ajout de rapport de diagnostic en un clic : télécharge la fin de chaque journal de composant et les instantanés de lancement pour faciliter la soumission de problèmes après reproduction.
- Ajout de la vérification d'état de montage ShadowMountPlus et du scan de réparation contrôlé.
- Ajout de sauvegarde, restauration, import et export de config ; la config actuelle est automatiquement conservée avant import ou restauration.
- Ajout de mise à jour sécurisée et retour arrière. Chemin ELF temporaire : /data/PS5Console/update/PS5_Console.elf. ELF vérifié avant installation ; version actuelle sauvegardée dans /data/etaHEN/plugins/PS5_Console.elf.bak.
- Ajout de l'historique de température CPU/GPU ; le navigateur sauvegarde les 60 derniers échantillons ; alerte haute température avec limitation quand le CPU atteint 80°C.
- Ajout du Centre des tâches : suit le scan de jeux, scan de manettes USB, réparation de montage, opérations de config, mises à jour sécurisées et diagnostics de lancement de jeu.

> Les mises à jour sécurisées ne remplacent pas à chaud le code Payload en cours d'exécution. Après le changement de fichier, arrêtez proprement le PS5 Console actuel et rechargez la nouvelle version via etaHEN ou ELF Loader.

### Mise à jour & Retour arrière Web

1. Ouvrez "Centre de contrôle → Santé système & Diagnostics → Mise à jour sécurisée & Retour arrière".
2. Cliquez sur "Téléverser un nouvel ELF" et sélectionnez votre PS5_Console.elf local.
3. Attendez que l'interface affiche "Version temporaire · Vérifiée".
4. Quittez les jeux et confirmez qu'aucune tâche de scan, montage, sauvegarde ou écriture FTP n'est en cours.
5. Cliquez sur "Installer la version temporaire" — le plugin actuel est automatiquement sauvegardé d'abord.
6. Arrêtez proprement le PS5 Console actuel, puis rechargez via etaHEN ou ELF Loader.

Si la nouvelle version ne fonctionne pas correctement, rechargez une version qui accède toujours à l'interface Web et cliquez sur "Retour à la version précédente". Vous pouvez aussi restaurer manuellement :

```
/data/etaHEN/plugins/PS5_Console.elf.bak
```

Le point de terminaison de téléversement n'accepte que les fichiers ELF bruts depuis le réseau local. Il ne télécharge pas de mises à jour depuis Internet et n'exécutera pas automatiquement de fichiers non confirmés.

### Compatibilité & Remarques

- Nécessite un environnement PS5 jailbreaké capable d'exécuter des Payloads, ainsi que etaHEN/kstuff correspondant à votre firmware.
- Si etaHEN inclut déjà kstuff, ne chargez pas de kstuff-lite séparé ; des patches noyau dupliqués peuvent causer des saccades, blocages ou erreurs système.
- N'activez pas simultanément le chargement automatique etaHEN, d'autres chargeurs automatiques de plugins et l'injection manuelle de PS5 Console.
- N'exécutez pas BackPort autonome parallèlement à la fonction fakelib/backport de ce projet.
- Monter des images, modifier des bases de données système et tester le PFS expérimental peuvent causer des anomalies d'arrêt ou une corruption de données.
- La suppression de sources de montage est irréversible ; sauvegardez avant de désinstaller, resigner, importer ou écraser des sauvegardes.
- Ne débranchez pas d'appareils USB, ne coupez pas l'alimentation et ne forcez pas l'arrêt du Payload pendant que des jeux ou sauvegardes sont lus/écrits.
- Si les jeux saccad, éliminez d'abord la surchauffe, les performances de stockage USB, les superpositions/plugins de triche et le kstuff dupliqué avant de supposer un échec de montage.

### FAQ

**L'icône du jeu existe mais le lancement échoue**

La mise en veille ou l'arrêt peut avoir nettoyé le montage de l'image. Confirmez que le stockage USB est reconnu, puis cliquez sur "Scanner de nouveaux jeux" dans le centre de contrôle et attendez que le montage et l'enregistrement se terminent.

**La manette USB ne répond pas après être entré dans le jeu**

La nouvelle version actualise automatiquement les liaisons après lancement du jeu. Si toujours non réactive, cliquez sur "Scanner les manettes USB" dans le centre de contrôle et vérifiez ghostcontrol.log pour des erreurs de création ou de liaison VDA. Si vous mettez à jour depuis une ancienne version et que le système signale toujours impossible de connecter plus de manettes, effectuez un arrêt complet pour nettoyer les anciennes ressources.

**Le jeu saccade avec une manette USB connectée**

Confirmez que vous utilisez le nouvel ELF avec l'optimisation du chemin critique GhostControl, videz les anciens logs et retestez. Désactivez également etaHEN Controller Shortcuts, Overlay, les superpositions FPS/température et les plugins de triche inutiles pour comparaison.

**La langue affiche toujours l'originale après changement**

Actualisez la page et videz l'ancien cache du site pour cette adresse PS5. L'interface dynamique est gérée par la nouvelle table de traduction ; une ancienne interface Web mise en cache par le navigateur peut continuer d'afficher d'anciens textes.

**La page Web ne s'ouvre pas**

Confirmez que le Payload est en cours d'exécution, que votre appareil est sur le même réseau local que la PS5 et que vous accédez au port 7777. Vérifiez également l'isolation client du routeur et les paramètres du pare-feu.

### Crédits & Projets amont

PS5 Console est construit sur les projets suivants et le travail de la communauté open-source PS5 :


- [ShadowMountPlus](https://github.com/drakmor/shadowMountPlus)
- [Garlic Save Manager](https://github.com/earthonion/garlic)
- GhostControl
- ps5-fan-control
- ftpsrv
- [etaHEN](https://github.com/etaHEN/etaHEN)
- [kstuff-lite](https://github.com/EchoStretch/kstuff-lite)
- [PS5 Payload SDK](https://github.com/ps5-payload-dev/sdk
Chaque composant continue de suivre sa licence d'origine et ses exigences d'attribution. Le code source fusionné, les licences amont et les avis originaux doivent être conservés avec les versions publiées.

---

## 🇯🇵 日本語

[⬆️ 言語選択に戻る](#ps5-console)

### 免責事項

本プロジェクトは「現状のまま」提供され、すべての PS5 ファームウェア、ハードディスク、ゲームイメージ、USB コントローラーとの互換性を保証するものではありません。作者および上流コントリビューターは、データ損失、システムエラー、アカウントリスク、ハードウェアの損傷、その他の結果について責任を負いません。使用前に重要なデータをバックアップし、関連操作のリスクを十分に理解してください。

### PS5 Console

ゲームマウント、セーブ管理、USB コントローラー互換性、FTP、ファン温度制御、ELF 管理を 1 つの Payload と Web UI に統合した、PS5 向けオールインワンローカルコントロールセンターです。

本プロジェクトは、Payload を実行可能な PS5 システム向けです。合法的に所有するゲームとセーブに対してのみ使用し、サードパーティ Payload のテスト、システムデータベースの変更、ゲームイメージのマウントに関する全責任を自己負担で引き受けてください。

### プロジェクト紹介

PS5 Console は複数のオープンソース PS5 プロジェクトを統合して開発されており、複数の Payload を個別に読み込む問題、設定が散在する問題、操作入口が統一されていない問題を解消することを目標としています。1 つの ELF を読み込むと、ブラウザから統合コントロールセンターにアクセスできます：

```
http://<PS5-IP>:7777/
```

現在統合されているコンポーネントと機能モジュール：

| コンポーネント / モジュール | 機能 |
| --- | --- |
| コントロールセンター | ゲームスキャン、USB コントローラー、ファン制御、サービス状態、よく使う操作を集中表示 |
| ShadowMountPlus | PS5 ゲームイメージのスキャン、マウント、登録。起動時スキャンと Web 手動スキャンに対応 |
| ゲーム管理 | ゲームの検索・表示、マウント元、登録状況の確認。PKG アンインストール、マウント元削除などの操作を提供 |
| マウントチェック＆修復 | システム登録済みゲームとメタデータ状態をチェック。制御された手動スキャンで無効なマウントを修復 |
| ゲームタイトル浄化 | ゲームタイトル末尾のドメイン広告を自動削除し、メタデータコピーと app.db タイトルを同期修正 |
| Garlic Save Manager | PS4/PS5 セーブの閲覧、復号、暗号化、再署名、インポート、バックアップ |
| GhostControl | 互換性のある USB コントローラーを PS5 仮想コントローラーにマッピング。スキャン、状態、再バインド、全解放を管理 |
| PS4 ゲームコントローラー再接続 | CUSA ゲーム起動後に VDA を安全に再構築・バインドし、初回起動時のコントローラー無応答を防止 |
| ftpsrv | PS5 ファイルアクセス＆転送サービス。デフォルト FTP ポート 2121 |
| ps5-fan-control | ファン目標温度の設定と CPU/GPU 温度の読み取り |
| 温度履歴＆高温アラート | 直近 60 回の CPU/GPU 温度サンプルを保存。CPU が 80°C に達するとスロットリング注意喚起 |
| ELF マネージャー | etaHEN プラグイン、Payload、ELF のスキャンと起動。PS5 Console 自身の再起動を防止 |
| システムヘルスチェック | 単一インスタンスロック、プラグインファイル、GhostControl VDA エラー、自己注入重複、LVD タイムアウト、高温を検出 |
| ゲーム起動診断 | ゲーム起動時に Title ID、温度、ファン、GhostControl 状態のスナップショットを記録 |
| ワンクリック診断レポート | コンポーネント状態、ログ末尾、ゲーム起動スナップショットを集約し、ダウンロード可能な診断レポートを生成 |
| 設定バックアップ＆リストア | 設定のバックアップ、リストア、インポート、エクスポートに対応。インポートまたはリストア前に現在のバージョンを自動保持 |
| 安全なアップデート＆ロールバック | ブラウザから新版 ELF をアップロード・検証。インストール前に現在のプラグインをバックアップし、ロールバックをサポート |
| タスクセンター | スキャン、マウント修復、設定、アップデート、診断タスクの進捗と最終結果を表示 |
| ログ管理 | Web ターミナルで実行情報を表示。二重確認で現在のログとローテーションログを消去 |
| 多言語 UI | 複数言語を切り替え可能。設定はブラウザローカルストレージに保存 |

### 主な機能

#### ゲームスキャンとマウント

- .ffpkg、.exfat、.ffpfs、.ffpfsc イメージに対応。
- PS5 の完全起動後、Payload 初回起動時に 1 回自動スキャン＆マウント。
- 同じ起動サイクル内での Payload 再読み込み、レストモード復帰では自動再スキャンしません。
- その他のスキャンは Web UI の「新しいゲームをスキャン」から手動で実行。
- カスタムスキャンディレクトリ、スキャン深度、読み取り専用マウント、強制マウント、fakelib/backport 設定に対応。
- ゲーム管理ページでマウント元の表示、ゲーム検索、PKG アンインストール、マウント元削除が可能。
- イメージメタデータ内のゲーム名末尾に付加されたウェブサイトドメインを自動浄化し、app.db の登録済みタイトルを同期修正。
- UFS .ffpkg の使用を推奨。外部ストレージ互換が必要な場合に限り .exfat を使用してください。手動で exFAT イメージを作成する場合は 64 KB クラスターサイズを推奨。PFS サポートは実験段階です。

#### USB コントローラーと GhostControl

- 起動時に接続済み USB コントローラーを 1 回自動スキャン。
- 起動後に新しく接続したコントローラーは Web UI の「USB コントローラーをスキャン」で手動認識可能。
- PS5 ゲーム起動後に仮想コントローラーと現在のユーザーのバインドを自動更新。抜き差し不要。
- PS4（CUSA）ゲーム起動後は入力システムの初期化を待ってから VDA を安全に再構築・バインドし、完全なコントローラー再接続イベントをシミュレート。
- レストモード前に USB エンドポイントと仮想コントローラーを解放し、復帰時にコントローラーバインドスロットが埋まるのを防止。
- バックグラウンドでの周期的 USB 列挙、高頻度成功ログ、周期的な物理コントローラーチェックを廃止し、ゲーム中の USB バス、CPU、ディスク書き込み負荷を低減。
- 単一インスタンスロックにより、重複注入で複数の GhostControl 管理スレッドが作成されるのを防止。

#### セーブツール

- PS4、PS5、ローカル、USB のセーブを閲覧可能。
- セーブの復号、暗号化、再署名、インポート、ダウンロード、削除に対応。
- ローカルディレクトリ、USB、FTP、Google Drive キュー/設定モードでのバックアップに対応。
- FTP バックアップは更新日時を比較し、変更がない場合は再アップロードをスキップ。
- ゲーム終了後のセーブ変化を監視し、自動バックアップを実行可能。

#### コントロールセンターと UI

- 多言語 UI 切り替え。言語設定はブラウザローカルストレージに保存。
- デスクトップ版メインナビゲーションはセンタリング、モバイル版は折りたたみメニュー。
- ファン温度制御有効時に CPU/GPU デュアル温度計を表示。
- ファンカードの「設定」ボタンで詳細設定のファンセクションへ直接ジャンプ。
- ターミナル領域で実行情報を表示。二重確認で全コンポーネントのログとローテーションログを消去可能。
- /data/etaHEN/plugins と /data/etaHEN/payloads 内の外部 ELF スキャンに対応。
- コントロールセンターから PS5 Console 自身を再起動することを禁止し、再帰注入を防止。

#### ファン温度制御

- 30–90°C の範囲で目標温度を設定可能。
- 再適用間隔と CPU/GPU 温度通知を設定可能。
- ファン速度は PS5 システムドライバーが管理。本プロジェクトは目標温度閾値のみを変更し、固定回転数やカスタムファンカーブは提供しません。

### 最新のアップデートと修正

#### 2026-07-13

**PS4 ゲーム USB コントローラー修正**

- CUSA ゲーム初回進入後に USB コントローラーが無応答となり、物理的な抜き差しと再スキャンが必要だった問題を修正。
- ゲーム起動約 4.5 秒後に該当スロットの入力を一時停止し、旧 VDA を削除。元の USB エンドポイントと読み取りスレッド上に新しい VDA を作成・バインド。
- VDA 作成失敗時は自動で 3 回リトライ。追加の USB スキャンは行わず、余分な仮想コントローラースロットを長期占有しません。
- PPSA ゲームは引き続き軽量なその場での再バインドを採用し、不要なデバイス再構築を回避。
- GhostControl にコンポーネントレベルの排他起動保護を追加し、複数管理スレッドの同時初期化を阻止。

**Web セーフアップデート**

- コントロールセンターから直接ローカルの PS5_Console.elf を選択してアップロード可能。事前に FTP でステージングする必要がなくなりました。
- アップロードサイズは 4 KB ～ 32 MB に制限。ELF64、Little Endian、AMD64、PIE/実行可能ファイル形式を検証。
- アップロードはまず .part ファイルに書き込まれ、完全受信と検証成功後にステージングバージョンをアトミックに置換。インストール時はプラグインディレクトリ内の .new ファイルで再検証し、現在の ELF をアトミックに置換。
- インストール前に現在のプラグインを /data/etaHEN/plugins/PS5_Console.elf.bak に自動バックアップ。ワンクリックロールバックに対応。
- アップデートはディスクファイルのみを置換し、実行中コードのホットスワップは行いません。インストールまたはロールバック後は現在のインスタンスを安全に終了し、etaHEN または ELF Loader で再読み込みする必要があります。

#### 2026-07-12

**USB コントローラーと安定性**

- レストモード後に仮想コントローラーが完全に解放されず、「これ以上 Bluetooth コントローラーを接続できません」エラーが発生する問題を修正。
- USB コントローラーがゲーム初回進入後に操作不能となり、抜き差しと再スキャンが必要だった問題を修正。
- GhostControl が重複読み込み後に複数の VDA コントローラースロットを占有する問題を修正。
- /data/PS5Console/PS5_Console.lock 単一インスタンスロックを追加。重複注入時は新しいインスタンスが即座に終了。
- レストモード前にスキャンを一時停止、エンドポイントを閉鎖、仮想デバイスを削除。
- ゲーム起動後は既存の VDA バインド更新を遅延実行し、余分な仮想コントローラーは作成しない。
- USB 自動列挙を起動時 1 回に変更。その他の操作は Web UI の手動スキャンで実行。
- 高頻度 VDI 成功ログ、周期的な物理コントローラーチェック、継続的な USB 列挙を削除し、USB コントローラー接続時のカクつきを改善。

**ゲームマウントとタイトル**

- レストモードでマウントがクリアされた後、ホーム画面にゲームアイコンは残るが起動に失敗する問題のトラブルシューティングフローを改善。
- 自動スキャンルールを完全起動ごとに 1 回に変更。レストモード復帰、Payload 再読み込み後は再スキャンしない。
- 同一システム起動セッションを識別する boot_scan.marker を追加。
- ゲームタイトル浄化を追加：タイトル末尾のドメイン広告を削除し、param.json コピーと app.db 登録済みタイトルを修正。
- 通常インストールと一括インストールの両パスでタイトル浄化を適用。元のゲームイメージは変更しません。

**ログ、設定、パフォーマンス**

- ログと設定を /data/PS5Console/ に統合。初回実行時に旧ファイルを自動移行。
- Web UI に「ログを消去」を追加。設定ファイルは削除されません。
- ファンスレッドの周期的な [FAN] threshold=... デバッグ出力を削除。
- レストモード、復帰、USB コントローラー再バインド、単一インスタンス遮断のログを強化。
- 旧設定の無効フィールドによる重複警告を修正。

**Web UI**

- 「Payload 管理」を「コントロールセンター」に改名。
- 多言語切り替えを追加し、動的状態、設定説明、ターミナルメッセージの翻訳を完備。
- CPU/GPU 温度計を追加（ファン温度制御有効時のみ表示）。
- 「USB コントローラーをスキャン」ボタンと多言語結果フィードバックを追加。
- 詳細設定コンテンツとデスクトップ版上部メインナビゲーションをセンタリング表示に変更。
- 終了プロンプトの帰属を By LeLe に修正。旧来の外部スポンサーボタンを削除。
- ファン「設定」ボタンで該当する詳細設定セクションへ直接移動可能に。

### インストールと使用方法

1. PS5_Console.elf を普段使いの Payload Loader または etaHEN プラグインディレクトリに配置します。
2. 同じ起動サイクルで有効にする自動読み込み方式は 1 つだけにしてください。etaHEN プラグインの自動読み込みが推奨です。
3. ELF を読み込み、PS5 Console 起動通知を待ちます。
4. 同じローカルネットワーク上のブラウザから `http://<PS5-IP>:7777/` にアクセスします。
5. 完全起動後の初回読み込みでは、ゲームと接続済み USB コントローラーが自動スキャンされます。以降は必要に応じて Web UI の手動スキャンを使用してください。

etaHEN プラグイン配置パスの例：

```
/data/etaHEN/plugins/PS5_Console.elf
```

新版 ELF に置き換える際は、先にゲームを終了し、進行中のスキャン、マウント、セーブ書き込みを停止してから、旧インスタンスを終了して再読み込みしてください。仮想コントローラースロットが旧バージョンから残留している場合は、完全にシャットダウンして一瞬電源を切り、旧バージョンが残したカーネルリソースをクリアしてください。

### ランタイムディレクトリ

新版は自身のログと設定を以下に集約保存します：

```
/data/PS5Console/
```

主なファイル：

```
/data/PS5Console/config.ini
/data/PS5Console/shadowmountplus.log
/data/PS5Console/ghostcontrol.log
/data/PS5Console/garlic.log
/data/PS5Console/ftpsrv.log
/data/PS5Console/elf_plugins.log
/data/PS5Console/sync_history.log
/data/PS5Console/boot_scan.marker
/data/PS5Console/PS5_Console.lock
```

一部の統合コンポーネントは独自の互換設定ファイルを保持する場合があります。Web UI に実際の有効パスが表示されます。アップデート後に診断が必要な場合は、まず UI から履歴ログを消去し、「起動 → スキャン → ゲーム起動 → 事象再現」の順序でテストして新しいログをエクスポートしてください。

### 2026-07-13 システムツールアップデート

- システムヘルスチェックを追加：単一インスタンスロック、プラグインファイル、GhostControl VDA エラー、自己注入遮断、実行中ゲーム、温度状態を集中表示。
- ゲーム起動診断スナップショットを追加。/data/PS5Console/game_diagnostics.log に自動書き込み。
- ワンクリック診断レポートを追加：各コンポーネントのログ末尾とゲーム起動スナップショットをダウンロードし、再現後の問題報告に便利。
- ShadowMountPlus マウント状態チェックと制御付き修復スキャンを追加。
- 設定バックアップ、リストア、インポート、エクスポートを追加。インポートまたはリストア前に現在の設定を自動保持。
- 安全なアップデートとロールバックを追加。ステージング ELF パス：/data/PS5Console/update/PS5_Console.elf。インストール前に ELF を検証し、現在のバージョンを /data/etaHEN/plugins/PS5_Console.elf.bak にバックアップ。
- CPU/GPU 温度履歴を追加。ブラウザに直近 60 サンプルを保存。CPU が 80°C に達するとスロットリング高温注意喚起。
- タスクセンターを追加：ゲームスキャン、USB コントローラースキャン、マウント修復、設定操作、安全アップデート、ゲーム起動診断を記録。

> 安全アップデートは実行中の Payload コードをホットスワップしません。ファイル切り替え完了後、現在の PS5 Console を安全に終了し、etaHEN または ELF Loader で新版を再読み込みしてください。

### Web アップデートとロールバック

1. 「コントロールセンター → システムヘルス＆診断 → 安全アップデート＆ロールバック」を開きます。
2. 「新版 ELF をアップロード」をクリックし、ローカルの PS5_Console.elf を選択します。
3. UI に「ステージングバージョン・検証済み」と表示されるまで待ちます。
4. ゲームを終了し、スキャン、マウント、セーブ、FTP 書き込みタスクが実行されていないことを確認します。
5. 「ステージングバージョンをインストール」をクリックします。現在のプラグインは先に自動バックアップされます。
6. 現在の PS5 Console を安全に終了し、etaHEN または ELF Loader で再読み込みします。

新版が正常に動作しない場合は、Web UI にアクセス可能なバージョンを再読み込みして「前のバージョンにロールバック」をクリックしてください。手動で復元することもできます：

```
/data/etaHEN/plugins/PS5_Console.elf.bak
```

アップロードエンドポイントはローカルネットワークからの生 ELF ファイルのみを受け付けます。インターネットからアップデートをダウンロードすることはなく、未確認のファイルを自動実行することもありません。

### 互換性と注意事項

- Payload を実行可能な脱獄済み PS5 環境と、ファームウェアに対応した etaHEN/kstuff が必要です。
- etaHEN に既に kstuff が統合されている場合は、別途 kstuff-lite を読み込まないでください。重複したカーネルパッチはカクつき、フリーズ、システムエラーの原因となる場合があります。
- etaHEN 自動読み込み、他のプラグイン自動読み込み、PS5 Console の手動注入を同時に有効にしないでください。
- スタンドアロンの BackPort と本プロジェクトの fakelib/backport 機能を同時に実行しないでください。
- イメージのマウント、システムデータベースの変更、実験的 PFS のテストは、シャットダウン異常やデータ破損を引き起こす可能性があります。
- マウント元の削除は元に戻せません。アンインストール、再署名、インポート、セーブ上書きの前には必ずバックアップしてください。
- ゲームやセーブの読み書き中は、USB デバイスを抜いたり、電源を切ったり、Payload を強制終了したりしないでください。
- ゲームがカクつく場合は、マウント失敗と決めつける前に、まず過熱、USB ストレージ性能、オーバーレイ/チートプラグイン、kstuff の重複を除外してください。

### よくある質問

**ゲームアイコンはあるのに起動に失敗する**

レストモードまたはシャットダウンの過程でイメージのマウントがクリアされた可能性があります。USB ストレージが認識されていることを確認し、コントロールセンターの「新しいゲームをスキャン」をクリックして、マウントと登録が完了するまで待ってください。

**USB コントローラーがゲームに入った後反応しない**

新版ではゲーム起動後にバインドを自動更新します。それでも反応しない場合は、コントロールセンターの「USB コントローラーをスキャン」をクリックし、ghostcontrol.log に VDA 作成またはバインドエラーがないか確認してください。旧バージョンからの初回アップグレードで、システムがまだ「これ以上コントローラーを接続できません」と表示する場合は、完全シャットダウンして旧リソースをクリアしてください。

**USB コントローラー接続時にゲームがカクつく**

GhostControl のホットパス最適化を含む新版 ELF を使用していることを確認し、旧ログを消去してから再テストしてください。また、比較のため etaHEN Controller Shortcuts、Overlay、FPS/温度オーバーレイ、不要なチートプラグインを無効にしてください。

**言語を切り替えても元の言語のまま**

ページを更新し、この PS5 アドレスの古いサイトキャッシュをクリアしてください。動的 UI は新しい翻訳テーブルで処理されますが、ブラウザが旧 Web UI をキャッシュしていると古い文言が表示され続ける場合があります。

**Web ページが開かない**

Payload が実行中であること、デバイスが PS5 と同じ LAN に接続されていること、ポート 7777 にアクセスしていることを確認してください。ルーターのクライアント分離とファイアウォール設定も確認してください。

### クレジットとアップストリームプロジェクト

PS5 Console は以下のプロジェクトと PS5 オープンソースコミュニティの成果に基づいて構築されています：


- [ShadowMountPlus](https://github.com/drakmor/shadowMountPlus)
- [Garlic Save Manager](https://github.com/earthonion/garlic)
- GhostControl
- ps5-fan-control
- ftpsrv
- [etaHEN](https://github.com/etaHEN/etaHEN)
- [kstuff-lite](https://github.com/EchoStretch/kstuff-lite)
- [PS5 Payload SDK](https://github.com/ps5-payload-dev/sdk

各コンポーネントは元のライセンスと帰属要件に従います。マージされたソースコード、アップストリームライセンス、元の通知はリリースと共に保持されるものとします。

---

## 🇷🇺 Русский

[⬆️ Назад к выбору языка](#ps5-console)

### Отказ от ответственности

Данный проект предоставляется «как есть» без гарантии совместимости со всеми прошивками PS5, жёсткими дисками, образами игр или USB-контроллерами. Автор и участники апстрим-проектов не несут ответственности за потерю данных, системные ошибки, риски для аккаунта, повреждение оборудования или любые другие последствия. Перед использованием сделайте резервную копию важных данных и полностью осознайте риски связанных операций.

### PS5 Console

Единый локальный центр управления для PS5, объединяющий монтирование игр, управление сохранениями, совместимость USB-контроллеров, FTP, контроль температуры вентилятора и управление ELF в одном Payload с веб-интерфейсом.

Данный проект предназначен для систем PS5, уже способных запускать Payload. Используйте его только с играми и сохранениями, которые вы законно владеете, и самостоятельно несёте полную ответственность за тестирование сторонних Payload, изменение системных баз данных и монтирование образов игр.

### Описание проекта

PS5 Console создан путём интеграции нескольких открытых проектов для PS5 и призван решить проблемы раздельной загрузки нескольких Payload, разрозненных настроек и неединых точек входа для операций. После загрузки одного ELF вы получаете доступ к единому центру управления через браузер:

```
http://<PS5-IP>:7777/
```

Текущие интегрированные компоненты и функциональные модули:

| Компонент / Модуль | Функция |
| --- | --- |
| Центр управления | Централизованное отображение сканирования игр, USB-контроллеров, управления вентилятором, статуса сервисов и часто используемых операций |
| ShadowMountPlus | Сканирование, монтирование и регистрация образов игр PS5; поддерживает сканирование при загрузке и ручное веб-сканирование |
| Управление играми | Поиск и просмотр игр, источников монтирования и статуса регистрации; операции удаления PKG, удаления источника монтирования |
| Проверка и восстановление монтирования | Проверка зарегистрированных в системе игр и состояния метаданных; исправление нерабочих монтирований через контролируемые ручные сканы |
| Очистка заголовков игр | Автоматически удаляет рекламные домены в конце названий игр и синхронно исправляет копии метаданных и заголовки в app.db |
| Garlic Save Manager | Просмотр, расшифровка, шифрование, переподписание, импорт и резервное копирование сохранений PS4/PS5 |
| GhostControl | Отображает совместимые USB-контроллеры на виртуальные контроллеры PS5; управляет сканированием, статусом, перепривязкой и полным освобождением |
| Переподключение геймпада в играх PS4 | Безопасно пересоздаёт и привязывает VDA после запуска игр CUSA, чтобы геймпад не переставал отвечать при первом запуске |
| ftpsrv | Сервис доступа и передачи файлов PS5; стандартный FTP-порт 2121 |
| ps5-fan-control | Задание целевой температуры вентилятора и чтение температур CPU/GPU |
| История температур и предупреждение о перегреве | Сохраняет последние 60 замеров температуры CPU/GPU; предупреждение о троттлинге при достижении CPU 80°C |
| Менеджер ELF | Сканирование и запуск плагинов etaHEN, Payload и ELF; предотвращает повторный запуск самого PS5 Console |
| Проверка состояния системы | Обнаруживает блокировку единственного экземпляра, файлы плагинов, ошибки VDA GhostControl, дублирующую самoinъекцию, таймаут LVD и перегрев |
| Диагностика запуска игры | Записывает снимки Title ID, температуры, вентилятора и состояния GhostControl при запуске игры |
| Диагностический отчёт в один клик | Объединяет статус компонентов, концы логов и снимки запуска игр в загружаемый диагностический отчёт |
| Резервное копирование и восстановление настроек | Поддерживает резервное копирование, восстановление, импорт и экспорт настроек; автоматически сохраняет текущую версию перед импортом или восстановлением |
| Безопасное обновление и откат | Загрузка и проверка новых версий ELF из браузера; резервное копирование текущего плагина перед установкой и поддержка отката |
| Центр задач | Отображает прогресс и итоговые результаты задач сканирования, восстановления монтирования, настроек, обновлений и диагностики |
| Управление логами | Просмотр информации о работе в веб-терминале; очистка текущих и ротируемых логов с двойным подтверждением |
| Многоязычный интерфейс | Переключение между несколькими языками; выбор сохраняется в локальном хранилище браузера |

### Основные возможности

#### Сканирование и монтирование игр

- Поддерживаются образы .ffpkg, .exfat, .ffpfs и .ffpfsc.
- Автоматическое сканирование и монтирование один раз при первом запуске Payload после полной загрузки PS5.
- Перезагрузка Payload или выход из режима ожидания в рамках одного цикла загрузки не вызывают повторных автоматических сканов.
- Дополнительные сканы запускаются вручную через пункт «Сканировать новые игры» в веб-интерфейсе.
- Поддерживаются пользовательские каталоги сканирования, глубина сканирования, монтирование только для чтения, принудительное монтирование и настройка fakelib/backport.
- На странице управления играми отображаются источники монтирования, поиск игр, удаление PKG и удаление источника монтирования.
- Автоматически очищает домены сайтов, добавленные к названиям игр в метаданных образов, и синхронно исправляет зарегистрированные заголовки в app.db.
- Рекомендуется в первую очередь использовать UFS .ffpkg. Используйте .exfat только при необходимости совместимости с внешним хранилищем; при ручном создании образов exFAT рекомендуется размер кластера 64 КБ. Поддержка PFS остаётся экспериментальной.

#### USB-контроллеры и GhostControl

- Автоматически сканирует подключённые USB-контроллеры один раз при загрузке.
- Новые контроллеры, подключённые после загрузки, можно опознать вручную через «Сканировать USB-контроллеры» в веб-интерфейсе.
- После запуска игры PS5 автоматически обновляет привязку виртуального геймпада к текущему пользователю — переподключение не требуется.
- После запуска игр PS4 (CUSA) дожидается инициализации системы ввода, затем безопасно пересоздаёт и привязывает VDA, имитируя полное событие переподключения геймпада.
- Освобождает USB-конечные точки и виртуальные контроллеры перед переходом в режим ожидания, чтобы не заполнять слоты привязки геймпадов при пробуждении.
- Устранена фоновая периодическая нумерация USB, высокочастотные логи успеха и периодические проверки физических геймпадов для снижения нагрузки на шину USB, процессор и запись на диск во время игры.
- Блокировка единственного экземпляра предотвращает создание нескольких управляющих потоков GhostControl при дублирующей инъекции.

#### Инструменты для сохранений

- Просмотр сохранений PS4, PS5, локальных и на USB.
- Поддерживаются расшифровка, шифрование, переподписание, импорт, загрузка и удаление сохранений.
- Поддерживается резервное копирование через локальный каталог, USB, FTP и режимы очереди/настроек Google Drive.
- FTP-резервное копирование сравнивает время изменения и пропускает повторную загрузку неизменённых файлов.
- Может отслеживать изменения сохранений после выхода из игры и выполнять автоматическое резервное копирование.

#### Центр управления и интерфейс

- Переключение многоязычного интерфейса; выбор языка сохраняется в локальном хранилище браузера.
- Главная навигация на десктопе по центру; на мобильных устройствах используется раскрывающееся меню.
- Два индикатора температуры CPU/GPU отображаются при включённом управлении вентилятором.
- Кнопка «Настройки» на карточке вентилятора ведёт напрямую в раздел вентилятора в расширенных настройках.
- В терминале отображается информация о работе; очистка всех логов компонентов и ротируемых логов с двойным подтверждением.
- Поддерживается сканирование внешних ELF в каталогах /data/etaHEN/plugins и /data/etaHEN/payloads.
- Запрещён повторный запуск самого PS5 Console из центра управления во избежание рекурсивной инъекции.

#### Управление температурой вентилятора

- Целевая температура задаётся в диапазоне 30–90°C.
- Настраиваемый интервал повторного применения и уведомления о температуре CPU/GPU.
- Скорость вентилятора по-прежнему управляется системным драйвером PS5; данный проект изменяет только порог целевой температуры и не предоставляет фиксированных оборотов или пользовательских кривых вентилятора.

### Последние обновления и исправления

#### 2026-07-13

**Исправление USB-геймпада в играх PS4**

- Исправлена проблема, при которой USB-геймпад переставал отвечать после первого запуска игр CUSA и требовал физического переподключения и повторного сканирования.
- Примерно через 4,5 секунды после запуска игры ввод на соответствующем слоте приостанавливается, старый VDA удаляется, новый VDA создаётся и привязывается на исходной USB-конечной точке и потоке чтения.
- При неудаче создания VDA выполняется 3 автоматических повтора без дополнительного сканирования USB и долговременного занятия дополнительных слотов виртуальных геймпадов.
- В играх PPSA продолжает использоваться более лёгкая перепривязка на месте во избежание избыточного пересоздания устройств.
- В GhostControl добавлена защита запуска с взаимным исключением на уровне компонента, предотвращающая одновременную инициализацию нескольких управляющих потоков.

**Безопасное веб-обновление**

- Из центра управления можно напрямую выбрать и загрузить локальный файл PS5_Console.elf — предварительное размещение через FTP больше не требуется.
- Размер загружаемого файла ограничен диапазоном 4 КБ – 32 МБ; проверяются типы ELF64, Little Endian, AMD64 и PIE/исполняемый файл.
- Загрузка сначала записывается в файл .part; атомарная замена промежуточной версии происходит только после полного приёма и успешной проверки. При установке выполняется повторная проверка через файл .new в каталоге плагинов, затем атомарно заменяется текущий ELF.
- Перед установкой текущий плагин автоматически резервируется как /data/etaHEN/plugins/PS5_Console.elf.bak; поддерживается откат в один клик.
- Обновление заменяет только файлы на диске и не выполняет горячую замену выполняемого кода. После установки или отката необходимо безопасно завершить текущий экземпляр и перезагрузить его через etaHEN или ELF Loader.

#### 2026-07-12

**USB-контроллеры и стабильность**

- Исправлена проблема, при которой виртуальные геймпады не полностью освобождались после режима ожидания, что вызывало ошибку «невозможно подключить больше Bluetooth-геймпадов».
- Исправлена проблема, при которой USB-геймпад переставал работать после первого входа в игру и требовал переподключения и повторного сканирования.
- Исправлена проблема, при которой GhostControl занимал несколько слотов VDA-геймпадов после повторной загрузки.
- Добавлена блокировка единственного экземпляра /data/PS5Console/PS5_Console.lock; при дублирующей инъекции новый экземпляр немедленно завершает работу.
- Перед переходом в режим ожидания сканирование приостанавливается, конечные точки закрываются, виртуальные устройства удаляются.
- Обновление существующих привязок VDA после запуска игры откладывается; дополнительные виртуальные геймпады не создаются.
- Автоматическая нумерация USB изменена на один раз при загрузке; все остальные операции используют ручное сканирование из веб-интерфейса.
- Удалены высокочастотные логи успеха VDI, периодические проверки физических геймпадов и непрерывная нумерация USB для снижения подтормаживаний при подключённых USB-геймпадах.

**Монтирование игр и заголовки**

- Улучшен процесс диагностики для игр, иконки которых остаются на главном экране, но запуск завершается неудачей после очистки монтирований режимом ожидания.
- Правило автоматического сканирования изменено на один раз за полную загрузку; повторное сканирование после режима ожидания или перезагрузки Payload не выполняется.
- Добавлен файл boot_scan.marker для идентификации одной и той же сессии загрузки системы.
- Добавлена очистка заголовков игр: удаляются рекламные домены в конце названий, исправляются копии param.json и зарегистрированные заголовки в app.db.
- Очистка заголовков применяется как для обычной, так и для пакетной установки; исходные образы игр не изменяются.

**Логи, настройки и производительность**

- Логи и настройки объединены в каталоге /data/PS5Console/; старые файлы автоматически мигрируют при первом запуске.
- В веб-интерфейсе добавлен пункт «Очистить логи» — файлы настроек при этом не удаляются.
- Удалена периодическая отладочная запись [FAN] threshold=... из потока вентилятора.
- Расширено логирование перехода в режим ожидания, пробуждения, перепривязки USB-геймпадов и блокировки дублирующих экземпляров.
- Исправлены дублирующиеся предупреждения, вызванные недействительными полями в старых настройках.

**Веб-интерфейс**

- Пункт «Управление Payload» переименован в «Центр управления».
- Добавлено многоязычное переключение с полными переводами динамических статусов, описаний настроек и сообщений терминала.
- Добавлены индикаторы температуры CPU/GPU (отображаются только при включённом управлении вентилятором).
- Добавлена кнопка «Сканировать USB-контроллеры» с многоязычной обратной связью о результате.
- Содержимое расширенных настроек и верхняя главная навигация на десктопе теперь отображаются по центру.
- Авторство в уведомлении о закрытии исправлено на By LeLe; удалена устаревшая кнопка внешнего спонсора.
- Кнопка «Настройки» у вентилятора ведёт напрямую в соответствующий раздел расширенных настроек.

### Установка и использование

1. Поместите PS5_Console.elf в предпочитаемый каталог Payload Loader или плагинов etaHEN.
2. Убедитесь, что за один цикл загрузки включён только один способ автозагрузки. Рекомендуется автозагрузка плагина etaHEN.
3. Загрузите ELF и дождитесь уведомления о запуске PS5 Console.
4. Откройте в браузере на той же локальной сети адрес `http://<PS5-IP>:7777/`.
5. При первой загрузке после полного старта автоматически сканируются игры и подключённые USB-контроллеры; в дальнейшем используйте ручное сканирование в веб-интерфейсе по мере необходимости.

Пример пути развертывания плагина etaHEN:

```
/data/etaHEN/plugins/PS5_Console.elf
```

При замене на новую версию ELF сначала выйдите из игры и остановите выполняющиеся сканирования, монтирования и записи сохранений, затем завершите старый экземпляр и перезагрузите. Если слоты виртуальных геймпадов остались от предыдущей версии, выполните полное выключение и кратковременно обесточьте консоль, чтобы очистить ресурсы ядра, оставшиеся старой версией.

### Каталог времени выполнения

Новая версия хранит свои логи и настройки централизованно в:

```
/data/PS5Console/
```

Распространённые файлы:

```
/data/PS5Console/config.ini
/data/PS5Console/shadowmountplus.log
/data/PS5Console/ghostcontrol.log
/data/PS5Console/garlic.log
/data/PS5Console/ftpsrv.log
/data/PS5Console/elf_plugins.log
/data/PS5Console/sync_history.log
/data/PS5Console/boot_scan.marker
/data/PS5Console/PS5_Console.lock
```

Некоторые интегрированные компоненты могут сохранять собственные файлы настроек для совместимости; в веб-интерфейсе отображаются фактические активные пути. Для диагностики после обновления сначала очистите историю логов через интерфейс, затем протестируйте и экспортируйте новые логи в последовательности: загрузка → сканирование → запуск игры → воспроизведение проблемы.

### Обновление системных инструментов 2026-07-13

- Добавлена проверка состояния системы: централизованно отображает блокировку единственного экземпляра, файлы плагинов, ошибки VDA GhostControl, блокировку самoinъекции, запущенные игры и статус температуры.
- Добавлены диагностические снимки запуска игр, автоматически записываемые в /data/PS5Console/game_diagnostics.log.
- Добавлен диагностический отчёт в один клик: загружает концы логов всех компонентов и снимки запуска игр для удобной отправки отчёта о проблеме после воспроизведения.
- Добавлена проверка статуса монтирования ShadowMountPlus и контролируемое сканирование восстановления.
- Добавлены резервное копирование, восстановление, импорт и экспорт настроек; текущие настройки автоматически сохраняются перед импортом или восстановлением.
- Добавлены безопасное обновление и откат. Путь промежуточного ELF: /data/PS5Console/update/PS5_Console.elf. ELF проверяется перед установкой; текущая версия резервируется как /data/etaHEN/plugins/PS5_Console.elf.bak.
- Добавлена история температур CPU/GPU; браузер сохраняет последние 60 замеров; предупреждение о перегреве с троттлингом при достижении CPU 80°C.
- Добавлен Центр задач: отслеживает сканирование игр, сканирование USB-контроллеров, восстановление монтирования, операции с настройками, безопасные обновления и диагностику запуска игр.

> Безопасные обновления не выполняют горячую замену выполняемого кода Payload. После переключения файлов безопасно завершите текущий PS5 Console и перезагрузите новую версию через etaHEN или ELF Loader.

### Веб-обновление и откат

1. Откройте «Центр управления → Состояние системы и диагностика → Безопасное обновление и откат».
2. Нажмите «Загрузить новый ELF» и выберите локальный файл PS5_Console.elf.
3. Дождитесь отображения в интерфейсе статуса «Промежуточная версия · Проверена».
4. Выйдите из игры и убедитесь, что не выполняются задачи сканирования, монтирования, сохранений или записи по FTP.
5. Нажмите «Установить промежуточную версию» — текущий плагин сначала автоматически резервируется.
6. Безопасно завершите текущий PS5 Console, затем перезагрузите через etaHEN или ELF Loader.

Если новая версия работает некорректно, перезагрузите версию, в которой веб-интерфейс ещё доступен, и нажмите «Откатить на предыдущую». Также можно восстановить вручную:

```
/data/etaHEN/plugins/PS5_Console.elf.bak
```

Точка загрузки принимает только исходные файлы ELF из локальной сети. Она не скачивает обновления из интернета и не выполняет автоматически неподтверждённые файлы.

### Совместимость и примечания

- Требуется взломанная среда PS5 с возможностью запуска Payload, а также etaHEN/kstuff, соответствующие вашей прошивке.
- Если etaHEN уже включает kstuff, не загружайте отдельный kstuff-lite; дублирующие патчи ядра могут вызывать подтормаживания, зависания или системные ошибки.
- Не включайте одновременно автозагрузку etaHEN, автозагрузку других плагинов и ручную инъекцию PS5 Console.
- Не запускайте автономный BackPort одновременно с функцией fakelib/backport данного проекта.
- Монтирование образов, изменение системных баз данных и тестирование экспериментального PFS могут вызывать аномальное завершение работы или повреждение данных.
- Удаление источников монтирования необратимо; делайте резервную копию перед удалением, переподписанием, импортом или перезаписью сохранений.
- Не отключайте USB-устройства, не отключайте питание и не принудительно завершайте Payload во время чтения/записи игр или сохранений.
- Если игра подтормаживает, прежде чем считать причиной сбой монтирования, исключите перегрев, проблемы с производительностью USB-хранилища, оверлеи/читы-плагины и дублирующий kstuff.

### Часто задаваемые вопросы

**Иконка игры есть, но запуск не проходит**

Режим ожидания или выключение могли очистить монтирование образа. Убедитесь, что USB-накопитель опознан, затем нажмите «Сканировать новые игры» в центре управления и дождитесь завершения монтирования и регистрации.

**USB-геймпад не реагирует после входа в игру**

В новой версии привязки автоматически обновляются после запуска игры. если геймпад всё равно не отвечает, нажмите «Сканировать USB-контроллеры» в центре управления и проверьте ghostcontrol.log на ошибки создания или привязки VDA. При первом обновлении со старой версии, если система по-прежнему сообщает, что невозможно подключить больше геймпадов, выполните полное выключение для очистки старых ресурсов.

**Игра подтормаживает при подключённом USB-геймпаде**

Убедитесь, что вы используете новый ELF с оптимизацией горячего пути в GhostControl, очистите старые логи и протестируйте заново. Также для сравнения отключите etaHEN Controller Shortcuts, Overlay, FPS/температурные оверлеи и ненужные читы-плагины.

**После переключения язык остаётся прежним**

Обновите страницу и очистите старый кэш сайта для данного адреса PS5. Динамический интерфейс обрабатывается новой таблицей переводов; при кэшировании браузером старого веб-интерфейса старые надписи могут отображаться и дальше.

**Веб-страница не открывается**

Убедитесь, что Payload запущен, ваше устройство находится в одной локальной сети с PS5 и вы обращаетесь к порту 7777. Также проверьте изоляцию клиентов в роутере и настройки брандмауэра.

### Благодарности и апстрим-проекты

PS5 Console создан на основе следующих проектов и работ открытого сообщества PS5:


- [ShadowMountPlus](https://github.com/drakmor/shadowMountPlus)
- [Garlic Save Manager](https://github.com/earthonion/garlic)
- GhostControl
- ps5-fan-control
- ftpsrv
- [etaHEN](https://github.com/etaHEN/etaHEN)
- [kstuff-lite](https://github.com/EchoStretch/kstuff-lite)
- [PS5 Payload SDK](https://github.com/ps5-payload-dev/sdk

Каждый компонент продолжает следовать своей исходной лицензии и требованиям к указанию авторства. Объединённый исходный код, лицензии апстрим-проектов и исходные уведомления должны сохраняться вместе с релизами.
