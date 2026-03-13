# OpenClaw Android App 完整安装教程

> 本教程整合自记忆文档和官方文档，提供从零开始安装 OpenClaw Android App 的完整指南。

**官方参考文档：**
- Android 文档：https://docs.openclaw.ai/platforms/android
- Nodes 文档：https://docs.openclaw.ai/nodes
- GitHub README：https://github.com/openclaw/openclaw/tree/main/apps/android/README.md
- 故障排除：https://docs.openclaw.ai/nodes/troubleshooting

---

## 一、项目概述

### 1.1 当前状态

**Status: extremely alpha** — App 正在积极重构中

### 1.2 已完成功能

- [x] 4 步 onboarding 引导流程
- [x] Connect 连接标签（Setup Code + Manual 模式）
- [x] Gateway 连接状态加密存储
- [x] Chat UI 重新设计
- [x] Settings UI 重新设计
- [x] QR 码扫描
- [x] 性能优化
- [x] 流式输出支持
- [x] 权限请求（相机、位置等）
- [x] 推送通知
- [x] 安全加固（生物锁、Token 处理）
- [x] Voice 语音功能
- [x] Screen 屏幕功能

---

## 二、技术栈

### 2.1 开发技术

| 技术 | 说明 |
|------|------|
| **语言** | Kotlin |
| **UI 框架** | Jetpack Compose |
| **最低 SDK** | API 31 (Android 12) |
| **目标 SDK** | API 36 (Android 16) |
| **编译 SDK** | 36 |
| **Java 版本** | JDK 17 |
| **构建工具** | Gradle (Kotlin DSL) |

### 2.2 主要依赖

| 库 | 用途 |
|------|------|
| Compose BOM 2026.02.00 | UI 框架 |
| CameraX | 相机功能 |
| OkHttp | 网络请求 |
| Security Crypto | 加密存储 |
| ZXing | QR 码扫描 |
| dnsjava | 局域网发现 (mDNS/NSD) |
| Bouncy Castle | 加密 |
| CommonMark | Markdown 解析 |

---

## 三、从源代码构建安装

OpenClaw Android App **没有发布到 GitHub Releases 或 Google Play Store**，需要从源代码构建。

### 3.1 环境要求

| 要求 | 说明 |
|------|------|
| **JDK 17+** | 通过 sdkman 或 Oracle 安装 |
| **Android SDK** | 通过 Android Studio 安装（推荐）或 Homebrew |

**方式一（推荐）：安装 Android Studio**
- 官网：https://developer.android.com/studio
- 安装后会自动配置 SDK

**方式二：Homebrew 安装**
```bash
brew install android-sdk
```

### 3.2 构建步骤

```bash
# 1. 克隆仓库
git clone https://github.com/openclaw/openclaw.git
cd openclaw/apps/android

# 2. 构建 Debug 版 APK（使用项目自带的 gradlew）
./gradlew :app:assembleDebug
```

### 3.3 安装 APK

构建完成后，APK 位于：
```
app/build/outputs/apk/debug/
```

传输到平板安装：
- 微信/QQ 文件传输助手
- 数据线拷贝
- 网盘

### 3.4 构建命令参考

```bash
cd apps/android

# 构建 Debug 版 APK
./gradlew :app:assembleDebug

# 安装到设备（需 USB 调试）
./gradlew :app:installDebug

# 运行单元测试
./gradlew :app:testDebugUnitTest

# Lint 检查
./gradlew :app:lintDebug
```

---

## 四、平板设置

### 4.1 启用开发者选项（小米平板 5 为例）

1. **进入设置** → **关于平板**
2. 找到 **MIUI 版本**，连续点击 7 次
3. 提示"您已处于开发者模式"
4. 返回设置 → 滑动到底部 → **更多设置** → **开发者选项**

### 4.2 开启 USB 调试

1. 进入 **开发者选项**
2. 开启 **USB 调试**
3. 首次开启会提示"允许 USB 调试吗"，点击**确定**

### 4.3 允许安装未知来源应用

1. **设置** → **安全** → 开启 **未知来源应用安装**
2. 或在安装 APK 时点击**设置** → 允许

### 4.4 连接 WiFi

确保平板和 Gateway 主机连接到**同一个 WiFi**

---

## 五、Gateway 配置（主机端）

### 5.1 启动 Gateway

```bash
openclaw gateway --port 18789 --verbose
```

### 5.2 验证启动

日志应显示：`listening on ws://0.0.0.0:18789`

### 5.3 获取主机局域网 IP

```bash
ifconfig | grep "inet "
```

记录 `192.168.x.x` IP 地址。

### 5.4 验证状态

```bash
openclaw status
```

---

## 六、Android App 连接配对

### 6.1 安装并打开 App

1. 安装下载的 APK
2. 打开 App，首次打开会请求权限，点击**允许**
3. 进入 **Connect**（连接）标签

### 6.2 连接方式

**方式 A：自动发现（同一 WiFi）**
- App 会自动搜索局域网中的 Gateway
- 如果找到，显示 Gateway 名称，点击连接

**方式 B：手动输入（推荐）**
- 点击 **Manual**
- 输入：
  - Host: `192.168.x.x`（主机局域网 IP）
  - Port: `18789`
- 点击 **Connect**

### 6.3 获取配对码

连接后会显示配对码（如 `ABC123`）

### 6.4 批准配对（主机端）

```bash
# 查看配对请求
openclaw devices list

# 批准配对
openclaw devices approve <requestId>

# 验证连接
openclaw nodes status
```

---

## 七、功能使用

### 7.1 Chat（聊天）

在平板 App 进入 **Chat** 标签发送消息，AI 会通过主机处理并回复。

### 7.2 Voice（语音）

1. 进入 **Voice** 标签
2. 按住麦克风按钮说话
3. 释放后发送，AI 回复通过 TTS 播放

**注意**：需在主机配置 ElevenLabs TTS 才有人声，否则用系统默认语音。

### 7.3 Camera（相机）

使用命令：
- `camera.snap` - 拍照
- `camera.clip` - 录像

**权限要求：**
- `camera.snap`: CAMERA
- `camera.clip`: CAMERA + RECORD_AUDIO

**注意**：App 必须在**前台**

### 7.4 Canvas（网页展示）

1. 在主机创建 canvas 目录：
   ```bash
   mkdir -p ~/.openclaw/workspace/canvas
   ```

2. 创建测试页面：
   ```bash
   echo "<h1>Hello from OpenClaw!</h1>" > ~/.openclaw/workspace/canvas/index.html
   ```

3. 在平板浏览器访问：
   ```
   http://192.168.x.x:18789/__openclaw__/canvas/
   ```

### 7.5 Screen（屏幕功能）

App 进入 **Screen** 标签，可用于屏幕录制和展示。

---

## 八、进阶：Tailscale 远程连接

当平板和主机**不在同一 WiFi**时使用（如外出时）。

### 8.1 安装 Tailscale

- Mac/PC：https://tailscale.com
- Android：从应用商店或 APK 安装

### 8.2 登录同一账号

两台设备登录**同一个 Tailscale 账号**

### 8.3 配置 Gateway

```bash
openclaw configure set gateway.bind tailnet
```

或手动编辑 `~/.openclaw/openclaw.json`：
```json5
{
  gateway: { bind: "tailnet" }
}
```

重启 Gateway：
```bash
openclaw gateway restart
```

### 8.4 获取 Tailscale IP

```bash
tailscale ip -4
```

### 8.5 平板连接

在 App 中：
- Host: 主机的 **Tailscale IP**
- Port: `18789`

---

## 九、USB 直连测试（开发用）

如果不想依赖局域网，可以用 USB 隧道：

**终端 A（Gateway）:**
```bash
pnpm openclaw gateway --port 18789 --verbose
```

**终端 B（USB 隧道）:**
```bash
adb reverse tcp:18789 tcp:18789
```

**App 连接设置:**
- Host: `127.0.0.1`
- Port: `18789`
- TLS: off

---

## 十、权限说明

### 10.1 发现权限

| Android 版本 | 权限要求 |
|-------------|----------|
| Android 13+ (API 33+) | `NEARBY_WIFI_DEVICES` |
| Android 12 及以下 | `ACCESS_FINE_LOCATION`（用于 NSD 扫描） |

### 10.2 通知权限

Android 13+ 需要：`POST_NOTIFICATIONS`

### 10.3 相机权限

| 功能 | 权限 |
|------|------|
| `camera.snap` | `CAMERA` |
| `camera.clip` | `CAMERA` + `RECORD_AUDIO` |

---

## 十一、架构说明

### 11.1 工作原理

```
┌─────────────────────────────────────────────────────────────┐
│                        网络                                  │
│                                                              │
│   ┌─────────────┐              ┌─────────────────┐        │
│   │  Android    │  ───WiFi───→ │    Gateway 主机  │        │
│   │  平板/手机   │              │  (Mac/Linux/PC)  │        │
│   │  (节点)      │←────────────│   OpenClaw AI   │        │
│   └─────────────┘              └─────────────────┘        │
│         ↓                            ↓                    │
│   运行 App                      运行 Gateway               │
│   连接 Gateway                  控制 AI                    │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### 11.2 角色分工

| 设备 | 角色 | 运行内容 |
|------|------|----------|
| Mac/Linux/PC | **Gateway 主机** | OpenClaw Gateway + AI |
| Android 设备 | **节点 (Node)** | OpenClaw Android App |

**注意**：Android 平板只是"终端"，不运行 Gateway，AI 决策由主机完成。

---

## 十二、相关命令汇总

| 命令 | 说明 |
|------|------|
| `openclaw gateway --port 18789` | 启动 Gateway |
| `openclaw status` | 查看状态 |
| `openclaw nodes status` | 查看节点 |
| `openclaw nodes describe --node <id>` | 查看节点详情 |
| `openclaw devices list` | 查看配对请求 |
| `openclaw devices approve <id>` | 批准设备 |
| `openclaw logs --follow` | 查看日志 |

---

## 相关文档

- [OpenClaw 官方文档](https://docs.openclaw.ai)
- [Getting Started](https://docs.openclaw.ai/start/getting-started)
- [配对文档](https://docs.openclaw.ai/channels/pairing)
- [Gateway 配置](https://docs.openclaw.ai/gateway/configuration)
