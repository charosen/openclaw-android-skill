# OpenClaw Android App 中文教程

> 来源：/Users/charosen/Repos/openclaw/apps/android/README.md
> 更新时间：2026-03-12

---

## 状态

Status: extremely alpha — App 正在积极重构中

## 目录

1. 功能清单
2. Android Studio 打开
3. 构建命令
4. 代码规范
5. 性能测试
6. 真机运行
7. 连接配对
8. 权限说明
9. 集成测试
10. 常见问题

---

## 一、构建和运行命令

### 基本构建

```bash
cd apps/android

# 构建 Debug 版 APK
./gradlew :app:assembleDebug

# 安装到设备
./gradlew :app:installDebug

# 运行单元测试
./gradlew :app:testDebugUnitTest
```

### Kotlin Lint + Format

```bash
# Lint 检查
pnpm android:lint

# 自动格式化
pnpm android:format
```

### 直接用 Gradle

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

## 二、真机运行（USB）

1. 手机开启开发者选项 + USB 调试
2. USB 连接电脑，手机上点击"信任"
3. 验证：`adb devices -l`
4. 安装运行：`pnpm android:install` / `pnpm android:run`

---

## 三、连接配对

1. 主机启动 Gateway：`pnpm openclaw gateway --port 18789`
2. App 连接：Connect → Setup Code / Manual
3. 主机批准：`openclaw devices list` + `openclaw devices approve <id>`

---

## 四、权限

- 发现：Android 13+ 需要 NEARBY_WIFI_DEVICES
- 通知：POST_NOTIFICATIONS
- 相机：CAMERA + RECORD_AUDIO

---

## 五、集成测试

```bash
pnpm android:test:integration
```

---

## 六、官方文档

- https://docs.openclaw.ai/platforms/android
