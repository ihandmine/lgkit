# Changelog

## v1.0.0

首个公开版本。

- Xposed 通道：规则集、SSL 绕过（Conscrypt / OkHttp / Apache / WebView / X5）、
  加解密日志、环境隐藏、设备标识伪装、虚拟定位、防退出、截屏解除、WebView 调试、
  行为/SP/点击/数据库/控件文本记录
- Gadget 通道：脚本模式（自动附加 Java bridge，支持普通 JS）、监听模式（一键复制连接命令）、
  加载时序（waitForSo / loadDelayMs / settleMs）、自定义 Gadget so 与版本显示
- 模拟位置页：系统级 mock（覆盖 native 地图）
- 日志：xposed.log / gadget.log 分通道，类型过滤、跟随、导出
- 首页：模块状态、设备标识与目标伪装值、开发者信息
