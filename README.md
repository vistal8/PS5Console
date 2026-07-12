# PS5 Console

一体化 PS5 本地控制中心，将游戏挂载、存档管理、USB 手柄兼容、FTP、风扇温控和 ELF 管理整合到一个 Payload 与 Web UI 中。

> 当前项目用于已具备 Payload 运行环境的 PS5。请仅处理你合法拥有的游戏与存档，并自行承担测试第三方 Payload、修改系统数据库和挂载镜像的风险。

## 项目介绍

PS5 Console 基于多个开源 PS5 项目整合开发，目标是减少多个 Payload 分别加载、配置分散和操作入口不统一的问题。加载一个 ELF 后，即可通过浏览器访问统一控制中心：

```text
http://<PS5-IP>:7777/
```

当前集成组件：

| 组件 | 作用 |
| --- | --- |
| ShadowMountPlus | 扫描、挂载并注册 PS5 游戏镜像 |
| Garlic Save Manager | 浏览、解密、加密、重签、导入及备份 PS4/PS5 存档 |
| GhostControl | 将兼容的 USB 手柄映射为 PS5 虚拟手柄 |
| ftpsrv | 提供 PS5 文件访问与传输服务 |
| ps5-fan-control | 设置风扇目标温度并读取 CPU/GPU 温度 |
| ELF 管理器 | 扫描和启动 etaHEN 插件、Payload 与 ELF |

最终构建文件名为：

```text
PS5_Console.elf
```

## 主要功能

### 游戏扫描与挂载

- 支持 `.ffpkg`、`.exfat`、`.ffpfs` 和 `.ffpfsc` 镜像。
- 每次 PS5 完整开机后，Payload 首次启动时自动扫描并挂载一次。
- 同一次开机内重载 Payload、待机唤醒均不会自动重复扫描。
- 其余扫描由 Web UI 的“扫描新游戏”手动触发。
- 支持自定义扫描目录、扫描深度、只读挂载、强制挂载和 fakelib/backport 配置。
- 游戏管理页可查看挂载来源、搜索游戏、卸载 PKG 或删除挂载源。
- 自动净化镜像元数据中附加在游戏名末尾的网站域名，并同步修正 `app.db` 中已注册的标题。

推荐优先使用 UFS `.ffpkg`。仅在游戏需要外置存储兼容方式时使用 `.exfat`；手动制作 exFAT 镜像时建议使用 `64 KB` 簇大小。PFS 支持仍属于实验功能。

### USB 手柄与 GhostControl

- 开机时自动扫描一次已连接的 USB 手柄。
- 开机后新连接的手柄可通过 Web UI“扫描 USB 手柄”手动识别。
- 游戏启动后自动刷新虚拟手柄与当前用户的绑定，无需重新插拔。
- 待机前释放 USB endpoint 和虚拟手柄，避免唤醒后占满手柄绑定槽位。
- 停止后台周期性 USB 枚举、高频成功日志和周期实体手柄检查，降低游戏中的 USB 总线、CPU 与磁盘写入负担。
- 单实例锁阻止重复注入创建多个 GhostControl 管理线程。

### 存档工具

- 浏览 PS4、PS5、本机和 USB 存档。
- 支持存档解密、加密、重签、导入、下载与删除。
- 支持本地目录、USB、FTP 和 Google Drive 队列/配置模式备份。
- FTP 备份可比较修改时间，未发生变化时跳过重复上传。
- 可监视游戏退出后的存档变化并执行自动备份。

### 控制中心与界面

- 中文与 English 界面切换，语言选择保存在浏览器本地存储中。
- 桌面端主导航居中，移动端使用折叠菜单。
- 风扇温控启用时显示 CPU/GPU 双温度仪表。
- 风扇卡片的“设置”按钮可直接跳转到高级设置中的风扇区域。
- 终端区域可查看运行信息，并通过二次确认清空所有组件日志及轮转日志。
- 支持扫描 `/data/etaHEN/plugins` 与 `/data/etaHEN/payloads` 中的外部 ELF。
- 禁止从控制中心再次启动 PS5 Console 自身，避免递归注入。

### 风扇温控

- 可设置 `30–90°C` 的目标温度。
- 可配置重应用间隔和 CPU/GPU 温度通知。
- 风扇仍由 PS5 系统驱动管理，本项目只修改目标温度阈值，不提供固定转速或自定义风扇曲线。

## 本次更新与修复

### 2026-07-12

#### USB 手柄与稳定性

- 修复待机后虚拟手柄未完全释放，导致“无法连接更多蓝牙手柄”的问题。
- 修复 USB 手柄首次进入游戏后无法操控、必须拔插并重新扫描的问题。
- 修复 GhostControl 被重复加载后占用多个 VDA 手柄槽位的问题。
- 增加 `/data/PS5Console/PS5_Console.lock` 单实例锁；重复注入时新实例直接退出。
- 待机前暂停扫描、关闭 endpoint 并删除虚拟设备。
- 游戏启动后延迟刷新现有 VDA 绑定，不创建额外虚拟手柄。
- USB 自动枚举改为开机一次，其余操作由 Web UI 手动扫描。
- 移除高频 VDI 成功日志、周期实体手柄检查和持续 USB 枚举，改善连接 USB 手柄后的卡顿。

#### 游戏挂载与标题

- 修复待机清理挂载后，主界面保留游戏图标但游戏无法启动的问题定位流程。
- 自动扫描规则改为每次完整开机一次；待机、重载 Payload 后不重复扫描。
- 新增 `boot_scan.marker`，识别同一次系统启动。
- 新增游戏标题净化：删除标题末尾的域名广告，并修正 `param.json` 副本及 `app.db` 已注册标题。
- 普通安装和批量安装路径均应用标题净化，不修改原始游戏镜像。

#### 日志、配置与性能

- 日志和配置统一迁移到 `/data/PS5Console/`，首次运行时自动迁移旧文件。
- Web UI 新增“清空日志”，不会删除配置文件。
- 移除风扇线程周期性 `[FAN] threshold=...` 调试输出。
- 增强待机、唤醒、USB 手柄重绑定和单实例拦截日志。
- 修复旧配置中无效字段导致的重复警告。

#### Web UI

- “Payload 管理管理”更名为“控制中心”。
- 新增中英文切换并补齐动态状态、设置说明和终端信息翻译。
- 新增 CPU/GPU 温度仪表，仅在风扇温控开启时显示。
- 新增“扫描 USB 手柄”按钮及中英文结果反馈。
- 高级设置内容和桌面端顶部主导航改为居中显示。
- 关闭提示署名更正为 `By LeLe`，移除遗留的外部赞助按钮。
- 风扇“设置”按钮可直接定位到对应高级设置区域。

## 安装与使用

1. 将 `PS5_Console.elf` 放入常用 Payload Loader 或 etaHEN 插件目录。
2. 确保同一次开机只启用一种自动加载方式，推荐使用 etaHEN 插件自动加载。
3. 加载 ELF，等待 PS5 Console 启动通知。
4. 在同一局域网的浏览器访问 `http://<PS5-IP>:7777/`。
5. 完整开机后的首次加载会自动扫描游戏与已连接的 USB 手柄；之后按需使用 Web UI 手动扫描。

etaHEN 插件部署路径示例：

```text
/data/etaHEN/plugins/PS5_Console.elf
```

替换新版 ELF 时，先退出游戏并停止正在进行的扫描、挂载和存档写入，再关闭旧实例并重新加载。已经发生虚拟手柄槽位残留时，应完整关机并断电片刻，以清除旧版本留下的内核资源。

## 运行目录

新版将自身日志和配置集中保存到：

```text
/data/PS5Console/
```

常见文件包括：

```text
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

部分集成组件可能保留各自的兼容配置文件；Web UI 会显示实际生效路径。升级后如需诊断，请先通过界面清空历史日志，再按“开机 → 扫描 → 启动游戏 → 复现问题”的顺序测试并导出新日志。

## 编译

需要 Linux/WSL2 和可用的 [PS5 Payload SDK](https://github.com/ps5-payload-dev/sdk)。默认 SDK 路径为：

```text
/opt/ps5-payload-sdk
```

仓库已准备好合并依赖对象时，可在项目根目录执行：

```bash
make clean
make -B \
  PS5_PAYLOAD_SDK=/opt/ps5-payload-sdk \
  MERGE_DEPS_DIR=merged_sources/build_deps \
  shadowmountplus-ghostcontrol-garlic-ftpsrv-cn-merged.elf
```

然后复制为发布文件名：

```bash
cp shadowmountplus-ghostcontrol-garlic-ftpsrv-cn-merged.elf ../PS5_Console.elf
```

如果修改了 Garlic Web UI 或 GhostControl，必须先重新生成相应嵌入对象；只重新链接旧对象不会把前端或手柄修改带入最终 ELF。

## 兼容性与注意事项

- 需要可运行 Payload 的越狱 PS5 环境，以及与固件匹配的 etaHEN/kstuff。
- etaHEN 已集成 kstuff 时，不要再单独加载另一份 kstuff-lite；重复内核补丁可能造成卡顿、冻结或系统错误。
- 不要同时启用 etaHEN 自动加载、其他插件自动加载和手动注入 PS5 Console。
- 不要同时运行独立 BackPork 与本项目的 fakelib/backport 功能。
- 挂载镜像、修改系统数据库及测试实验性 PFS 可能导致关机异常或数据损坏。
- 删除挂载源不可恢复；卸载、重签、导入或覆盖存档前请先备份。
- 游戏或存档正在读写时，不要拔出 USB 设备、断电或强制结束 Payload。
- 若游戏卡顿，请先排除过热、USB 存储性能、叠加层/作弊插件以及重复 kstuff，而不是直接认定为挂载失败。

## 常见问题

### 游戏图标存在，但启动失败

待机或关机流程可能已经清理镜像挂载。确认 USB 存储已识别，然后在控制中心点击“扫描新游戏”，等待挂载和注册完成。

### USB 手柄进入游戏后没有反应

新版会在游戏启动后自动刷新绑定。若仍无反应，请在控制中心点击“扫描 USB 手柄”，并检查 `ghostcontrol.log` 中是否有 VDA 创建或绑定错误。首次从旧版升级且系统仍提示无法连接更多手柄时，需要完整关机清理旧资源。

### 连接 USB 手柄后游戏卡顿

确认使用的是包含 GhostControl 热路径优化的新版 ELF，并清空旧日志后重新测试。同时关闭 etaHEN Controller Shortcuts、Overlay、FPS/温度叠加层和不必要的作弊插件进行对照。

### 切换英文后仍显示中文

刷新页面并清除该 PS5 地址的旧站点缓存。动态界面由新版翻译表处理，浏览器缓存旧 Web UI 时可能继续显示旧文案。

### 网页打不开

确认 Payload 已运行、设备与 PS5 位于同一局域网，并访问端口 `7777`。同时检查路由器客户端隔离和防火墙设置。

## 致谢与上游项目

PS5 Console 建立在以下项目及 PS5 开源社区工作的基础上：

- [ShadowMountPlus](https://github.com/drakmor/shadowMountPlus)
- [Garlic Save Manager](https://github.com/earthonion/garlic)
- GhostControl
- ps5-fan-control
- ftpsrv
- [etaHEN](https://github.com/etaHEN/etaHEN)
- [kstuff-lite](https://github.com/EchoStretch/kstuff-lite)
- [PS5 Payload SDK](https://github.com/ps5-payload-dev/sdk)

各组件继续遵循其原始许可证与署名要求。合并源码、上游许可证和原始说明应随发布内容一并保留。

## 免责声明

本项目按“原样”提供，不保证适用于所有 PS5 固件、硬盘、镜像或 USB 手柄。作者及上游贡献者不对数据丢失、系统错误、账号风险、硬件损坏或其他后果负责。使用前请备份重要数据，并充分了解相关操作的风险。
