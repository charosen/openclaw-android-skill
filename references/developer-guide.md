# OpenClaw Android App 完整教程

> 仓库地址：https://github.com/openclaw/openclaw
> Android 目录：/Users/charosen/Repos/openclaw/apps/android
> 更新时间：2026-03-12

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

## 三、项目结构

```
apps/android/
├── README.md                    # 主文档
├── style.md                    # UI 设计规范
├── build.gradle.kts            # 根构建配置
├── settings.gradle.kts          # Gradle 设置
├── gradle.properties           # Gradle 属性
├── gradlew / gradlew.bat       # Gradle 包装器
├── gradle/                    # Gradle 包装器文件
├── .gitignore
├── app/                        # 主应用模块
│   ├── build.gradle.kts        # App 构建配置
│   ├── proguard-rules.pro     # ProGuard 混淆规则
│   └── src/                   # 源代码
│       └── main/
│           ├── java/ai/openclaw/app/
│           └── assets/
├── benchmark/                  # 性能基准测试
├── scripts/                   # 性能测试脚本
│   ├── perf-startup-benchmark.sh
│   └── perf-startup-hotspots.sh
└── THIRD_PARTY_LICENSES/
```

---

## 四、本地构建教程

### 4.1 环境要求

| 要求 | 说明 |
|------|------|
| **JDK** | JDK 17+ |
| **Android SDK** | ~/Library/Android/sdk (macOS 默认) |
| **Node.js** | 22+ (用于 pnpm) |
| **pnpm** | 包管理器 |

### 4.2 设置 Android SDK

如果未设置环境变量，Gradle 会自动检测：
- macOS: `~/Library/Android/sdk`
- 可设置 `ANDROID_SDK_ROOT` 或 `ANDROID_HOME` 环境变量

### 4.3 构建命令

#### 构建 Debug 版 APK

```bash
cd apps/android
./gradlew :app:assembleDebug
```

APK 输出位置：`apps/android/app/build/outputs/apk/debug/`

#### 安装到设备

```bash
cd apps/android
./gradlew :app:installDebug
```

#### 运行测试

```bash
cd apps/android
./gradlew :app:testDebugUnitTest
```

---

## 五、Android Studio 开发

### 5.1 用 Android Studio 打开

1. 打开 Android Studio
2. 选择 **Open**
3. 选择文件夹 `apps/android`

### 5.2 运行到真机

```bash
# 1. 确保手机已开启开发者选项 + USB 调试
# 2. 连接 USB 并在手机上信任调试
# 3. 验证设备
adb devices -l

# 4. 安装并运行
pnpm android:install
pnpm android:run
```

### 5.3 USB 直连测试（无需局域网）

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

## 六、快速迭代开发

### 6.1 热重载 (Live Edit)

对于 Compose UI 改动：
- 使用 Android Studio 的 **Live Edit** 功能（需 debug build）
- 项目 `minSdk=31` 已满足 API 要求

### 6.2 Apply Changes

对于非结构性代码/资源改动：
- 使用 Android Studio **Apply Changes**

### 6.3 完整重装

对于结构性/原生/manifest/Gradle 改动：
- 需要完整重装：`pnpm android:run`

### 6.4 Canvas 热重载

Canvas 网页内容从 Gateway 加载，支持热重载：
- Gateway: `__openclaw__/canvas/`

---

## 七、代码规范

### 7.1 Kotlin Lint + Format

```bash
# Lint 检查
pnpm android:lint

# 自动格式化
pnpm android:format
```

### 7.2 直接用 Gradle

```bash
cd apps/android

# Lint 检查
./gradlew :app:ktlintCheck :benchmark:ktlintCheck

# 格式化
./gradlew :app:ktlintFormat :benchmark:ktlintFormat

# 完整 Lint
./gradlew :app:lintDebug
```

---

## 八、连接 Gateway

### 8.1 启动 Gateway（主机端）

在运行 OpenClaw 的电脑上：

```bash
pnpm openclaw gateway --port 18789 --verbose
```

### 8.2 Android App 连接

1. 打开 App 的 **Connect** 标签
2. 选择 **Setup Code** 或 **Manual** 模式
3. Setup Code: 自动发现局域网 Gateway
4. Manual: 手动输入 Host 和 Port

### 8.3 批准配对（主机端）

```bash
# 查看配对请求
openclaw devices list

# 批准配对
openclaw devices approve <requestId>
```

---

## 九、权限说明

### 9.1 发现权限

| Android 版本 | 权限要求 |
|-------------|----------|
| Android 13+ (API 33+) | `NEARBY_WIFI_DEVICES` |
| Android 12 及以下 | `ACCESS_FINE_LOCATION`（用于 NSD 扫描） |

### 9.2 通知权限

Android 13+ 需要：`POST_NOTIFICATIONS`

### 9.3 相机权限

| 功能 | 权限 |
|------|------|
| `camera.snap` | `CAMERA` |
| `camera.clip` | `CAMERA` + `RECORD_AUDIO` |

---

## 十、性能测试

### 10.1 Macrobenchmark

```bash
cd apps/android
./gradlew :benchmark:connectedDebugAndroidTest
```

报告位置：`apps/android/benchmark/build/reports/androidTests/connected/`

### 10.2 Perf CLI（低噪音）

**启动测量：**
```bash
cd apps/android
./scripts/perf-startup-benchmark.sh
```

- 运行 10 次冷启动
- 输出中位数/最小值/最大值/COV
- 保存到 `apps/android/benchmark/results/`

**热点分析：**
```bash
cd apps/android
./scripts/perf-startup-hotspots.sh
```

- 使用 `simpleperf` 捕获启动数据
- 输出顶级 DSO、符号、关键路径

---

## 十一、集成测试

### 11.1 前置条件

1. Gateway 已运行且可达
2. Android App 已连接并配对
3. App 保持解锁且前台
4. 打开 **Screen** 标签
5. 授予所需权限
6. 无待处理系统对话框
7. Canvas host 已启用

### 11.2 运行测试

```bash
pnpm android:test:integration
```

### 11.3 可选参数

```bash
OPENCLAW_ANDROID_GATEWAY_URL=ws://...
OPENCLAW_ANDROID_GATEWAY_TOKEN=...
OPENCLAW_ANDROID_GATEWAY_PASSWORD=...
OPENCLAW_ANDROID_NODE_ID=...
OPENCLAW_ANDROID_NODE_NAME=...
```

---

## 十二、UI 设计规范

### 12.1 设计方向

- 干净、安静的画面
- 强可读性
- 每个屏幕一个主要动作
- 渐进式展示高级控制

### 12.2 配色方案

| 用途 | 颜色 |
|------|------|
| 背景渐变 | #FFFFFF, #F7F8FA, #EFF1F5 |
| Surface | #F6F7FA |
| 边框 | #E5E7EC |
| 主文字 | #17181C |
| 次要文字 | #4D5563 |
| 强调色 | #1D5DD8 |
| 成功 | #2F8C5A |
| 警告 | #C8841A |

### 12.3 字体

- 主字体：**Manrope** (400/500/600/700)
- 代码字体：等宽字体（Monospace）

---

## 十三、打包发布

### 13.1 Debug 版

```bash
./gradlew :app:assembleDebug
```

输出：`openclaw-2026.3.11-debug.apk`

### 13.2 Release 版

需要配置签名：

1. 在 `~/.gradle/gradle.properties` 中设置：
```properties
OPENCLAW_ANDROID_STORE_FILE=~/keystore/openclaw.keystore
OPENCLAW_ANDROID_STORE_PASSWORD=your_store_password
OPENCLAW_ANDROID_KEY_ALIAS=your_key_alias
OPENCLAW_ANDROID_KEY_PASSWORD=your_key_password
```

2. 构建：
```bash
./gradlew :app:assembleRelease
```

### 13.3 版本信息

| 版本 | 版本码 |
|------|--------|
| 2026.3.11 | 202603110 |

---

## 十四、常见问题

### ❌ USB 调试未授权

**解决：**
```bash
adb devices -l
```
如果显示 `unauthorized`，重新插拔 USB 并在手机上点击"信任"

### ❌ 找不到设备

**解决：**
1. 确认已开启开发者选项 + USB 调试
2. 重新插拔 USB
3. 在手机上确认"允许 USB 调试"

### ❌ 权限被拒绝

**解决：**
在设置中手动授予权限，或在测试时授予

---

## 十五、相关文档

- [官方 Android 文档](https://docs.openclaw.ai/platforms/android)
- [Getting Started](https://docs.openclaw.ai/start/getting-started)
- [配对文档](https://docs.openclaw.ai/channels/pairing)
- [Gateway 配置](https://docs.openclaw.ai/gateway/configuration)

---

## 十六、总结

OpenClaw Android App 是一个基于 **Kotlin + Jetpack Compose** 的原生应用，需要：

1. **JDK 17+** 和 **Android SDK**
2. 用 **Gradle** 构建
3. 通过 **USB 或局域网** 连接 Gateway
4. 配对后才能使用

App 功能包括：
- Chat（聊天）
- Voice（语音）
- Screen（屏幕展示）
- Camera（相机）
- 连接 Gateway

如需安装到小米平板，需要先构建 APK，然后传输到平板安装。
