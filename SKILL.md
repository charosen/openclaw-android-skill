---
name: openclaw-android-skill
description: OpenClaw Android App 安装配置技能。用于在 Android 设备（平板/手机）上安装、配置 OpenClaw 节点 App，连接到 Gateway 并使用 Chat/Voice/Camera/Canvas/Screen 功能。包括：安装方式、平板设置、Gateway 配置、连接配对、Tailscale 远程连接、常见问题与故障排除。触发场景：用户询问 Android 安装、节点配置、App 连接、配对问题、相机/语音功能、远程连接等。
---

# OpenClaw Android App 安装技能

## 快速开始

### 1. 安装 App（从源代码构建）

OpenClaw Android App **没有发布到 GitHub Releases 或 Google Play Store**，需要从源代码构建：

**环境要求：**
- JDK 17+（通过 sdkman 或 Oracle 安装）
- Android SDK（通过 [Android Studio](https://developer.android.com/studio) 安装，或 `brew install android-sdk`）

**构建步骤：**

```bash
# 1. 克隆仓库
git clone https://github.com/openclaw/openclaw.git
cd openclaw/apps/android

# 2. 构建 Debug 版 APK（项目自带 Gradle）
./gradlew :app:assembleDebug

# 3. 安装到设备（需 USB 调试开启）
./gradlew :app:installDebug
```

APK 位于：`app/build/outputs/apk/debug/`

或直接通过 USB 调试安装（无需手动传输）。

### 2. 平板设置

1. **开发者选项**：设置 → 关于平板 → 连续点击 MIUI 版本 7 次
2. **USB 调试**：开发者选项 → USB 调试
3. **未知来源**：设置 → 安全 → 未知来源应用安装

### 3. 启动 Gateway（主机）

```bash
openclaw gateway --port 18789 --verbose
```

### 4. App 连接

1. 打开 App → Connect 标签
2. 扫描主机上的配对 QR 码：
   ```bash
   openclaw qr
   ```
   或手动输入：Host `192.168.x.x`、Port `18789`
3. 获取配对码

### 5. 批准配对（主机）

```bash
openclaw devices list
openclaw devices approve <requestId>
openclaw nodes status
```

---

## 功能使用

| 功能 | 说明 | 注意 |
|------|------|------|
| Chat | 聊天 | Connect 后在 Chat 标签发送 |
| Voice | 语音 | Voice 标签按住麦克风 |
| Camera | 相机 | `camera.snap` 拍照，`camera.clip` 录像，需前台 |
| Canvas | 网页 | 浏览器访问 `http://<host>:18789/__openclaw__/canvas/` |
| Screen | 屏幕 | Screen 标签，需前台 |

---

## Tailscale 远程连接

1. 两台设备安装 Tailscale，登录同一账号
2. 主机：`openclaw configure set gateway.bind tailnet`
3. 获取 Tailscale IP：`tailscale ip -4`
4. 平板 App 连接使用 Tailscale IP

---

## 故障排除

**常见问题**：
- APK 无法安装 → 开启未知来源应用安装
- 找不到 Gateway → 同一 WiFi / 手动输入 IP / 检查防火墙
- 相机失败 → App 前台 + 授予 CAMERA/RECORD_AUDIO 权限
- canvas 失败 → App 前台 + 主机创建 canvas 目录
- 位置失败 → 授予位置权限 + App 设置启用

**诊断命令**：
```bash
openclaw status
openclaw nodes status
openclaw nodes describe --node <node-id>
openclaw devices list
openclaw logs --follow
```

**详细故障排除**：参见 [troubleshooting.md](references/troubleshooting.md)

---

## 参考资料

- **完整安装教程**：参见 [installation-guide.md](references/installation-guide.md)
- **开发者指南**：参见 [developer-guide.md](references/developer-guide.md)
- **故障排除**：参见 [troubleshooting.md](references/troubleshooting.md)
- **官方文档**：https://docs.openclaw.ai/platforms/android
- **Nodes 文档**：https://docs.openclaw.ai/nodes
