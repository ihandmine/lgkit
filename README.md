# LGKit

Android 逆向调试工具箱（LSPosed 模块）：Xposed Hook、Frida Gadget 注入、系统模拟位置、脚本与日志。

> 本仓库只发布编译产物与使用说明，**不提供源码**。

## 功能

### Xposed 通道（无需加载 so）

- 规则集：按 `class#method` 配置 `log / skip / return`，支持重载、调用栈
- 内置能力：
  - `ssl`：SSL Pinning 绕过（Conscrypt / OkHttp / Apache HttpClient / WebView / X5）
  - `crypto`：加解密出入参与密钥日志，带限流与明文优先
  - `hide`：包列表、su 路径、调试标志、代理/VPN、网卡过滤
  - `props`：机型 / 序列号 / IMEI / MAC / ANDROID_ID 伪装
  - `mockloc`：Java 层虚拟定位（仅影响读 `android.location.Location` 的 app）
  - `block`：拦截 `System.exit` / `killProcess`
  - `screen`：解除 FLAG_SECURE
  - `webdebug`：允许 chrome://inspect
  - `behavior` / `sp` / `click` / `db` / `uitext`：行为、SharedPreferences、点击、SQLite、控件文本记录
- 脚本：支持普通 JS（Rhino），可运行时开关、随开随关

### Gadget 通道

- 由模块在目标进程内加载 Frida Gadget，不依赖额外的 Zygisk 模块
- 两种模式：
  - **脚本模式**：启动时加载指定脚本；支持导入普通 `.js`，App 自动附加 Java bridge，直接写 `Java.perform`
  - **监听模式**：`frida -H <手机IP>:<端口> -n Gadget -l <脚本>`，页面可直接复制命令
- 加载时序：`waitForSo` / `loadDelayMs` / `settleMs`，用于与安全库错开初始化
- 文件管理：显示当前 Gadget 版本与大小，支持导入自定义 so、一键恢复内置

### 模拟位置

- 系统级模拟位置：走 Android 定位服务，Google Maps 等 native 地图也会跟随
- 需要 root 授予 AppOp，开启期间全设备生效，用完即关
- 不适用于百度/高德这类自研定位 SDK 的 app

### 日志与工具

- 分通道日志：`xposed.log` / `gadget.log`，支持类型过滤、跟随、导出
- 首页展示设备标识，以及目标 app 配置的伪装值
- 一键重启目标、撤销部署

### 命令行客户端

Xposed 模式下除了界面，还可以用命令行下发脚本、看实时日志。客户端不随 APK 打包，
在 Release 的 `lgkit-clients-v1.0.0.zip` 里：

- **lgnet**：网络长连接（Go 静态二进制，5 个平台）。把目标的「lgnet 端口」设为非 0，
  force-stop 重启后：
  ```bash
  ./lgnet -H <手机IP>:27045 -l script.js      # 挂住看实时日志
  ./lgnet -H <手机IP>:27045 -e 'console.log(1)' --once
  ```
- **lgctl**：文件轮询通道（bash + adb + root），支持 `ps / log / run / classes`

## 环境要求

- Android 8.0+（minSdk 26），arm64 设备
- LSPosed（legacy 模块）
- Root（SukiSU / KernelSU / Magisk），用于部署与部分系统功能
- 电脑端可选：`frida` 客户端（监听模式）

## 安装

1. 安装 APK，在 LSPosed 中启用模块并勾选需要作用的目标 app
2. 打开 LGKit → 「+ 添加」选择目标，自动写入配置与作用域
3. **新作用域需要重启设备**（LSPosed 只在开机时读取一次作用域）
4. 在 Xposed / Gadget 页分别配置两条通道，force-stop 目标后生效

## 使用提示

- Xposed 与 Gadget 是两条独立通道，可以只开一条，也可以都开
- Gadget 脚本模式默认「自动附加 Java bridge」，选已编译的 bundle 时请关掉
- 系统模拟位置开启后定位会被标记为模拟（`isMock`），部分 app 会拒绝使用
- 日志与配置都在目标的 `app_libs` 目录下，撤销部署会一并清理

## 常见问题

**Q：Gadget 连不上？**
检查端口是否被占用、`frida -H` 地址是否为手机当前 Wi-Fi IP（页面复制命令时会自动刷新）。

**Q：改了配置没生效？**
配置在目标启动时读取，需要 force-stop 后重新打开；新部署的目标还需要重启设备。

**Q：虚拟定位对百度地图无效？**
百度地图直读自研定位 SDK，不经过系统定位服务，属于已知限制。

**Q：会被检测到吗？**
任何注入方案都有特征。本项目提供端口隔离、加载时序、无 JVMTI 等兼容手段，但不承诺对所有人检测免疫。

## 第三方组件

- [Frida](https://frida.re) — wxWindows Library Licence（LGPL 2.1 + 例外条款），Gadget 二进制随包分发，源码与许可见官网
- SSL 绕过实现参考 [JustTrustMePro](https://github.com/Fuzion24/JustTrustMe)（Apache-2.0）的思路
- Rhino — Mozilla Public License 2.0

## 免责声明

本工具仅供**已获得授权**的安全研究、应用调试与自有应用测试使用。
使用者应自行确保行为符合当地法律法规及目标应用的服务条款。
作者不对因使用本工具造成的任何直接或间接损失负责。

## 开发者

ihandmine · [GitHub](https://github.com/ihandmine)
