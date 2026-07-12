<div align="center">

# 🎮 PS5 Console Payload 管理器

**PS5 Console** 是一个面向 PS5 的 Payload、游戏和存档综合管理器。它将四个核心组件合并到一个 ELF 中，并统一管理外部 etaHEN 插件、Payload 和 ELF。

<details>
<summary><strong>🇬🇧 English Description</strong></summary>

**PS5 Console** is an all-in-one manager for PS5 Payloads, Games, and Saves. It merges four core components into a single ELF and manages external etaHEN plugins, payloads, and ELFs.

</details>

当前 Web UI 版本 / Current Web UI Version: **6.1.0**

</div>

---

### 📦 核心组件

| 组件 | 功能描述 |
| :--- | :--- |
| **ShadowMountPlus** | 游戏镜像扫描、注册和挂载。 |
| **Garlic SaveMgr** | PS4/PS5 存档浏览、解密、加密、重签、导入及云备份。 |
| **ftpsrv** | PS5 FTP 文件服务。 |
| **Ghostcontrol** | USB 手柄兼容与控制功能。 |

<details>
<summary><strong>🇬🇧 Core Components (English)</strong></summary>

| Component | Description |
| :--- | :--- |
| **ShadowMountPlus** | Game image scanning, registration, and mounting. |
| **Garlic SaveMgr** | Save browsing, decryption, encryption, resigning, importing, and cloud backup. |
| **ftpsrv** | PS5 FTP file service. |
| **Ghostcontrol** | USB controller compatibility and control features. |

</details>

---

### 📜 主要改动总览

- **四合一合并**: 将 ShadowMountPlus、GhostControl、Garlic Save Manager、ftpsrv 合并为一个可加载 ELF，统一使用 `PS5 Console` 品牌和中文通知。
- **统一端口**: Web 端口统一为 `7777`。
- **UI 优化**: 桌面端改为左侧导航，手机端使用可滚动汉堡菜单。
- **主题切换**: 默认使用 PS5 蓝色视觉风格，支持白、黑、绿主题并可保存选择。
- **冲突解决**: 解决了 `-backpork` 与 `Ghostcontrol` 的冲突问题。
- **状态卡片**: 首页提供模块即时启动、重启、禁用状态保存和鲜艳的绿色/红色状态卡片。
- **游戏管理增强**: ShadowMountPlus 改为网页手动扫描，避免持续扫描扩展硬盘；增加封面式管理、搜索及 PKG 卸载。
- **存档工具整合**: 统一到一个菜单，补充手机滚动和下拉菜单遮挡修复。
- **云备份功能**: 增加游戏关闭后的存档变化监视、近期同步记录和 FTP 增量上传。
- **插件管理**: 扫描 etaHEN 插件、Payload 和 ELF，支持启动、重启、启用及随机启动配置。
- **日志统一**: 所有组件日志统一写入 `/data/sgfcnlog/`。

<details>
<summary><strong>🇬🇧 Changelog Overview (English)</strong></summary>

- **All-in-One Merge**: Merged ShadowMountPlus, GhostControl, Garlic Save Manager, and ftpsrv into a single loadable ELF with unified `PS5 Console` branding.
- **Unified Port**: Web port unified to `7777`.
- **UI Optimization**: Desktop uses a fixed left sidebar; mobile uses a scrollable hamburger menu.
- **Theme Switching**: Default PS5 blue theme, supporting White, Black, and Green themes with saved preferences.
- **Conflict Resolution**: Resolved conflicts between `-backpork` and `Ghostcontrol`.
- **Status Cards**: Home page features instant start/restart, disable state saving, and vivid green/red status cards.
- **Game Management Enhanced**: ShadowMountPlus switched to manual web scanning to reduce disk activity; added cover-grid management, search, and PKG uninstall.
- **Save Tools Integrated**: Unified into one menu with fixes for mobile scrolling and dropdown obstruction.
- **Cloud Backup**: Added save change monitoring after game close, recent sync logs, and FTP incremental upload.
- **Plugin Management**: Scans etaHEN plugins, payloads, and ELFs, supporting start, restart, enable, and random start configurations.
- **Unified Logging**: All component logs are unified to `/data/sgfcnlog/`.

</details>

---

### 📅 更新动态 (7.12)

#### 1. GhostControl / USB 手柄修复

- **修复待机问题**: 修复待机后 USB 虚拟手柄未完全释放，导致 PS5 提示“无法连接更多蓝牙手柄”的问题。待机前自动暂停 GhostControl，并释放 USB endpoint 和虚拟手柄。
- **优化扫描逻辑**: 唤醒后不再自动反复扫描 USB 手柄，避免重复创建虚拟设备。增加系统级单实例锁，阻止 PS5 Console 被重复注入。
- **新增手动扫描**: 控制中心新增“扫描 USB 手柄”按钮，支持中英文状态反馈。开机后的新 USB 手柄改为通过 Web UI 手动扫描。
- **修复游戏内控制**: 修复首次进入游戏后 USB 手柄无法控制的问题。检测到游戏启动后延迟约 1.2 秒，自动将现有虚拟手柄重新绑定到当前用户。
- **性能优化**: 移除高频 VDI 成功日志，停止每两秒持续枚举 USB，降低 USB 总线、CPU 和日志写入压力。

<details>
<summary><strong>🇬🇧 USB Controller Fixes (English)</strong></summary>

- **Fix Standby Issue**: Fixed virtual USB controller not fully releasing after standby, causing "Cannot connect more Bluetooth controllers" errors. GhostControl now pauses automatically before standby.
- **Optimize Scan Logic**: No longer auto-scans repeatedly after wake to avoid duplicate virtual devices. Added system-level single-instance lock to prevent duplicate injection.
- **Manual Scan Added**: Added "Scan USB Controllers" button in Control Center. New USB controllers after boot must be scanned manually via Web UI.
- **Fix In-Game Control**: Fixed USB controller unresponsiveness on first game launch. Automatically rebinds existing virtual controllers to the current user with a ~1.2s delay after game launch.
- **Performance**: Removed high-frequency VDI success logs and stopped 2-second USB enumeration intervals to reduce bus/CPU load.

</details>

#### 2. 游戏扫描与挂载逻辑

- **调整自动扫描**: PS5 每次完整开机后，Payload 首次启动自动扫描并挂载一次。同一次开机内重新加载 Payload 不自动扫描。待机唤醒后不自动扫描。其他扫描操作全部由 Web UI 手动触发。
- **修复扫描错误**: 修复待机清理游戏挂载后，重复加载 Payload 可能错误执行自动扫描的问题。

<details>
<summary><strong>🇬🇧 Game Scanning & Mounting (English)</strong></summary>

- **Auto-Scan Adjustment**: Auto-scans once on first payload launch after full boot. Does not auto-scan on payload reload or standby wake. Other scans are manual via Web UI.
- **Fix Scan Error**: Fixed issue where reloading payload after standby cleanup might trigger incorrect auto-scans.

</details>

#### 3. 游戏标题广告净化

- **自动净化**: 扫描时自动删除标题末尾的网站域名广告（例如 `Metro Exodus-2468c.com` → `Metro Exodus`）。
- **同步修正**: 同步修正 `app.db` 中已经注册的污染标题。新安装游戏的 `param.json` 副本也会自动净化。
- **规则说明**: 规则只处理标题末尾明显的域名，保留正常数字、版本号及标题内容。

<details>
<summary><strong>🇬🇧 Title Ad Purification (English)</strong></summary>

- **Auto-Purify**: Automatically removes domain ads from title endings during scan (e.g., `Metro Exodus-2468c.com` → `Metro Exodus`).
- **Sync Fix**: Corrects polluted titles in `app.db`. New game `param.json` copies are also purified.
- **Rule**: Only removes obvious domains at the end; keeps normal numbers, versions, and title content.

</details>

#### 4. 日志和配置文件整理

- **路径统一**: 所有日志与配置文件统一迁移到 `/data/PS5Console/`。首次启动新版时自动迁移旧配置和日志。
- **新增功能**: 控制中心终端区域新增“清空日志”按钮，支持中英文，可清理当前日志及轮转日志。

<details>
<summary><strong>🇬🇧 Log & Config Organization (English)</strong></summary>

- **Path Unification**: All logs and configs moved to `/data/PS5Console/`. Old configs migrate automatically on first launch.
- **New Feature**: Added "Clear Logs" button in Control Center terminal (supports CN/EN).

</details>

#### 5. Web UI 修改

- **名称变更**: “Payload 管理管理”统一改名为“控制中心”。
- **语言切换**: 高级设置新增中文 / English 界面语言切换，通过浏览器 `localStorage` 保存。
- **UI 细节**: 补齐英文模式下的主要界面、动态状态、设置说明及终端日志翻译。风扇温度控制卡片的“设置”按钮现在会自动滚动到风扇设置区域。
- **温度仪表**: “扫描新游戏”上方新增 CPU、GPU 双温度仪表（仅在风扇温度控制开启时显示）。

<details>
<summary><strong>🇬🇧 Web UI Changes (English)</strong></summary>

- **Rename**: "Payload Management" renamed to "Control Center".
- **Language Switch**: Added CN/EN toggle in Advanced Settings, saved via browser `localStorage`.
- **UI Details**: Completed EN translations. Fan control "Settings" button now auto-scrolls to the fan section.
- **Temp Gauges**: Added CPU/GPU temp gauges above "Scan New Games" (visible when fan control is on).

</details>

#### 6. 风扇与性能问题检查

- **移除调试信息**: 确认原合并版会周期性输出风扇调试信息，现已移除。
- **性能警告**: 日志曾记录 CPU 温度达到约 86°C、GPU 达到约 70°C，存在热降频风险。
- **精简热路径**: GhostControl 原有的高频 USB 枚举、输入日志和周期检查也可能造成卡顿，相关热路径已精简。
- **kstuff 建议**: etaHEN 已集成 kstuff，不建议同时单独加载另一份 kstuff-lite，避免重复内核补丁导致卡顿、冻结或系统错误。

<details>
<summary><strong>🇬🇧 Fan & Performance Check (English)</strong></summary>

- **Remove Debug**: Removed periodic fan debug output from the original merged version.
- **Performance Warning**: Logs showed CPU ~86°C and GPU ~70°C, risking thermal throttling.
- **Optimize Hot Path**: Refined high-frequency USB enumeration and input logs in GhostControl that caused lag.
- **kstuff Advice**: etaHEN includes kstuff. Do not load `kstuff-lite` separately to avoid kernel conflicts and freezes.

</details>

#### 7. 构建与最终文件

所有修改均已使用上传的 PS5 Payload SDK 重新编译，并生成完整合并版：

- ShadowMountPlus
- GhostControl
- Garlic Save Manager
- ftpsrv
- fan-control

<details>
<summary><strong>🇬🇧 Build & Files (English)</strong></summary>

All modifications recompiled with PS5 Payload SDK into a full merged version:

- ShadowMountPlus
- GhostControl
- Garlic Save Manager
- ftpsrv
- fan-control

</details>

---

### 🚀 快速开始

1. 将最终 ELF 注入或加载到 PS5。
2. 启动通知显示 `PS5 Console 已启动` 后，在同一局域网设备打开：
   ```text
   http://<PS5-IP>:7777/
