# PS5 Console Payload 管理器

`PS5 Console` 是一个面向 PS5 的 Payload、游戏和存档综合管理器。它既将四个核心组件合并到一个 ELF，也管理外部 etaHEN 插件、Payload 和 ELF：

当前 Web UI 版本：**6.1.0**。

- **ShadowMountPlus**：游戏镜像扫描、注册和挂载。
- **Garlic SaveMgr**：PS4/PS5 存档浏览、解密、加密、重签、导入及云备份。
- **ftpsrv**：PS5 FTP 文件服务。
- **Ghostcontrol**：USB 手柄兼容与控制功能。

### Payload 管理范围

- 管理内嵌 `ShadowMountPlus / Garlic SaveMgr / ftpsrv / Ghostcontrol` 的启动状态。
- 扫描并管理 `/data/etaHEN/plugins`、`/data/etaHEN/payloads` 中的插件、Payload 和 ELF。
- 支持立即启动、重启、启用、禁用、随机启动和自定义显示名称。
- 自动识别 V52 游戏扫描、PS5 -backpork、Websrv、FTP 文件服务和万能手柄等常见功能。
- 游戏管理、PKG 卸载、ShadowMountPlus 挂载源删除也属于本管理器的游戏 Payload 管理能力。

最终文件：

```text
PS5_Console.elf
```

## 主要改动总览

- 将四个项目合并为一个可加载 ELF，并统一使用 `PS5 Console` 品牌和中文通知。
- Web 端口统一为 `7777`，桌面改为左侧导航，手机使用可滚动汉堡菜单。
- 默认使用 PS5 蓝色视觉风格，支持白、黑、绿主题并保存选择。
- 解决了-backpork与Ghostcontrol冲突问题。
- 首页提供模块即时启动、重启、禁用状态保存和鲜艳的绿色/红色状态卡片。
- ShadowMountPlus 改为网页手动扫描，避免持续扫描扩展硬盘。
- 增加扫描目录配置、受支持游戏文件管理、重命名和删除。
- 增加封面式游戏管理、搜索、安装来源识别、PKG 卸载和挂载源删除。
- 增加 PS5 系统版本、内部存储及 `ext1` 剩余空间显示。
- 存档工具统一到一个菜单，补充手机滚动和下拉菜单遮挡修复。
- 增加游戏关闭后的存档变化监视、近期同步记录和 FTP 增量上传。
- 扫描 etaHEN 插件、Payload 和 ELF，支持启动、重启、启用及随机启动配置。
- 所有组件日志统一写入 `/data/sgfcnlog/`。

## 1. 快速开始

1. 将最终 ELF 注入或加载到 PS5。
2. 启动通知显示 `PS5 Console 已启动` 后，在同一局域网设备打开：

```text
http://<PS5-IP>:7777/
```

3. 桌面浏览器使用固定左侧菜单；手机使用顶部汉堡菜单。
4. 默认界面使用 PS5 蓝色主题。可在首页底部切换 `PS5 / 白 / 黑 / 绿`。

> 旧版本保存过黑色主题时，新版首次打开会自动迁移为 PS5 蓝色，之后继续记住所选主题。

## 2. 界面和菜单

### Payload 管理首页

首页提供大按钮卡片：

- **扫描新游戏**：手动触发 ShadowMountPlus 扫描。
- **ftpsrv**：立即启动或保存为禁用。
- **Ghostcontrol**：立即启动或保存为禁用。
- **etaHEN plugins / payloads / ELF**：扫描配置目录并显示插件卡片。

模块开启时卡片显示鲜艳绿色，关闭时显示红色。`重启/启动` 会重新请求启动对应模块。

关闭模块主要保存 `enabled=false`，使其下次不自动启动。已经运行且没有可靠停止接口的内嵌模块，可能需要重新注入 Payload 或重启 PS5 环境后才完全停止。

### 存档工具

左侧的“存档工具”包含：

- **浏览**：查看存档、挂载、下载、删除和文件管理。
- **解密**：导出可编辑的解密存档。
- **加密**：把处理后的文件重新生成加密存档。
- **重签**：修改存档账户归属。
- **导入**：导入加密或解密存档。

支持 PS4、PS5、本机存档和 USB 存档。执行覆盖、删除、重签前应先备份。

### 游戏管理

游戏管理使用封面网格，支持按游戏名、Title ID 和源路径搜索。

来源判断规则：

- 扫描到 `.exfat`、`.ffpfsc`、`.ffpkg` 或 `.phu` 源文件时，优先标记为 **ShadowMountPlus 挂载**。
- 未匹配到 ShadowMountPlus 源文件的可卸载 `app.db` 项标记为 **PKG 安装**。
- 扫描目录、完整源路径和 Title ID 会去重，避免同一游戏重复出现。

每张卡右上角的三点菜单提供操作：

- **卸载 PKG**：卸载游戏本体，不删除系统存档。
- **删除挂载文件**：永久删除 ShadowMountPlus 源文件，此操作不可恢复。
- 找不到挂载源文件时仅显示注册状态并禁用删除。

游戏封面读取自 PS5 AppMeta。没有封面时显示占位图。

## 3. ShadowMountPlus

### 手动扫描

ShadowMountPlus 不再由网页持续实时扫描。需要查找新游戏时，在首页点击“扫描新游戏”。这样可减少长时间扫描对 USB 或扩展硬盘的持续访问。

高级设置可配置：

- 扫描目录 `scanpath`
- 扫描深度
- 只读挂载
- 强制挂载
- 安静模式
- 写盘稳定等待时间
- 重载配置和停止 ShadowMountPlus

配置文件：

```text
/data/shadowmount/config.ini
```

网页文件管理只列出受支持的游戏源文件：

```text
.exfat  .ffpfsc  .ffpkg  .phu
```

支持重命名和删除。删除属于永久操作。

## 4. 模块和 ELF 管理

高级设置可以分别配置 `PS5 Console Web`、`ftpsrv` 和 `Ghostcontrol`：

- **启用**：Payload 启动时直接启动该模块。
- **随机**：仅在启用时参与随机启动逻辑。

只勾选“启用”而不勾选“随机”，表示每次直接启动。首页按钮用于当前会话立即启动和保存启用状态。

默认扫描目录包括：

```text
/data/etaHEN/plugins
/data/etaHEN/payloads
```

网页会扫描插件、Payload 和 ELF，并把启动或重启请求发送给本机 ELF Loader。必须先运行兼容的 `elfldr`；否则网页会报告 Loader 连接失败。

配置文件：

```text
/data/garlic/elf_plugins.json
```

## 5. 存档云备份

支持以下目标：

- PS5 本地目录
- USB 目录
- FTP
- Google Drive 浏览器队列/配置模式

云配置文件：

```text
/data/garlic/cloud.json
```

### FTP 增量备份

FTP 使用固定的远端文件名，并通过 FTP `MDTM` 比较远端时间与本地存档源文件的最新修改时间：

- 本地较新：重新打包并上传。
- 时间相同：跳过上传。
- 远端更新：跳过上传。
- FTP 不支持 `MDTM` 或远端文件不存在：正常上传。

网页和同步日志会显示“存档未变化，跳过上传”。旧版本生成的带时间戳 ZIP 不会自动删除；首次运行新版会建立固定名称备份，之后才进行增量比较。

自动备份需要：

1. 启用云备份及至少一个备份目标。
2. FTP 目标需单独勾选启用，仅把提供方选为 FTP 不代表目标已启用。
3. `watch_titles` 留空表示全部；填写后只有列表中的 Title ID 才自动备份。
4. 游戏关闭或回到主页后，生命周期监视器等待存档写盘稳定，再检查变化并开始备份。

## 6. 状态和日志

网页右下角显示真实 PS5 系统版本。首页底部显示内部存储和 `ext1` 的容量与剩余空间。

统一日志目录：

```text
/data/sgfcnlog/
```

主要日志：

```text
/data/sgfcnlog/shadowmountplus.log
/data/sgfcnlog/garlic.log
/data/sgfcnlog/ftpsrv.log
/data/sgfcnlog/ghostcontrol.log
/data/sgfcnlog/sync_history.log
/data/sgfcnlog/elf_plugins.log
```

查看方式：

- 首页点击“查看近期同步日志”。
- 使用 ftpsrv 下载 `/data/sgfcnlog/`。
- 通过其他 PS5 文件管理工具直接读取日志文件。

排错时建议提供操作时间、Title ID、网页提示，以及对应日志末尾内容。

## 7. 常见问题

### 网页打不开

- 确认启动通知中的网页端口为 `7777`。
- 确认设备与 PS5 在同一局域网。
- 检查路由器客户端隔离和防火墙。
- 若高级设置禁用了 PS5 Console Web，需要修改配置或重新部署恢复。

### FTP 测试提示目标未启用

在高级设置中同时启用云备份和 FTP 备份目标。错误 `FTP backup target is not enabled` 属于配置问题，不是网络连接失败。

### 自动备份提示不在 watch_titles

把 Title ID 加入监视列表，或清空监视列表以允许所有游戏。

### 游戏管理出现重复

新版按扫描根目录、完整路径和 Title ID 去重，并让 ShadowMountPlus 来源优先。部署新版 ELF 后刷新页面；若浏览器仍显示旧界面，强制刷新或清除该 PS5 地址的站点缓存。

### 扫描目录无法读取

检查目录是否存在、设备是否挂载、路径是否重复，以及 PS5 是否有权限访问。无效目录应从高级设置中删除。

### 插件点击启动失败

确认 `elfldr` 已运行、端口兼容，且 ELF 文件位于已配置目录。详细结果见 `/data/sgfcnlog/elf_plugins.log`。

### 游戏启动后 kstuff 状态

高级设置新增“游戏启动时自动暂停 kstuff”开关，对应：

```ini
kstuff_game_auto_toggle=0
```

默认值为 `0`，任何 PS4/PS5、PKG、ShadowMountPlus 或 backport 游戏都不会自动暂停 kstuff。设为 `1` 后，受监视游戏启动时会按配置延迟暂停，退出后恢复；`kstuff_no_pause=TITLE_ID` 仍可排除单个游戏。



## 8. 重要安全说明

- 删除 ShadowMountPlus 源文件不可恢复。
- PKG 卸载不等于删除存档，但操作前仍建议备份。
- 重签、导入、覆盖存档前先创建本地或 FTP 备份。
- 修改数据库、挂载参数或扫描目录前保留配置副本。
- 不要在游戏仍在写入存档时断电、拔盘或强制停止 Payload。

---

## English Quick Reference

PS5 Console merges ShadowMountPlus, Garlic SaveMgr, ftpsrv, and Ghostcontrol into one payload. Load `shadowmountplus-ghostcontrol-garlic-ftpsrv-cn-merged.elf`, then open `http://<PS5-IP>:7777/`.

- Desktop uses a fixed left sidebar; mobile uses a hamburger menu.
- The default theme is PS5 blue. White, black, and green themes are available.
- Use **Scan New Games** manually; continuous web scanning is disabled to reduce disk activity.
- Game Manager prioritizes ShadowMountPlus source files, deduplicates paths and Title IDs, and offers PKG uninstall or source-file deletion from the three-dot menu.
- FTP backup uses stable filenames and `MDTM`: it uploads only when local save data is newer.
- Logs are stored in `/data/sgfcnlog/`.
- ShadowMountPlus config: `/data/shadowmount/config.ini`.
- Cloud config: `/data/garlic/cloud.json`.
- ELF/plugin config: `/data/garlic/elf_plugins.json`.

Always back up saves before resigning, importing, overwriting, uninstalling, or deleting source files.
