# OpenClaw Android 安装问题与经验汇总

> 本文档记录在安装 OpenClaw Android App 过程中实际遇到的坑、问题及解决方案，结合官方故障排除指南。

**官方故障排除文档：** https://docs.openclaw.ai/nodes/troubleshooting

---

## 一、安装过程中的坑

### 坑1：APK 无法安装

**问题表现**：点击 APK 提示"无法安装"

**原因**：未开启未知来源应用安装

**解决步骤**：
1. 打开 **设置** → **安全**
2. 开启 **未知来源应用安装**
3. 或在安装时点击**设置** → 允许

---

### 坑2：平板找不到 Gateway

**问题表现**：App 无法自动发现 Gateway

**检查清单**：
- [ ] 平板和主机连的是**同一个 WiFi**
- [ ] Gateway 正在运行
- [ ] 防火墙未阻止

**解决**：
```bash
# 主机上临时关闭防火墙（测试用）
sudo pfctl -d

# 或手动输入主机 IP 连接
```

---

### 坑3：USB 调试找不到设备

**问题表现**：`adb devices` 显示空或 unauthorized

**解决步骤**：
1. 确认平板已开启 **USB 调试**
2. 用数据线连接平板和电脑
3. 平板上弹出"允许 USB 调试吗"，点击**允许**
4. 运行 `adb devices -l` 验证

**如果显示 unauthorized**：
- 重新插拔 USB
- 在手机上点击"信任"

---

### 坑4：配对失败

**问题表现**：连接后无法配对

**解决**：
```bash
# 查看详细错误
openclaw devices list -v

# 重新发起配对
openclaw devices approve <requestId>
```

---

### 坑5：权限被拒绝

**问题表现**：功能无法使用

**解决**：在平板设置中手动授予权限

---

### 坑6：camera.snap / camera.clip 失败

**问题表现**：拍照/录像命令执行失败

**官方错误码**：`CAMERA_PERMISSION_REQUIRED`、`RECORD_AUDIO_PERMISSION_REQUIRED`

**原因**：
1. App 在后台
2. 缺少 CAMERA 权限
3. 缺少 RECORD_AUDIO 权限（录像时）

**解决**：
1. 确保 App 在**前台**
2. 在设置中授予 **CAMERA** 权限
3. 录像还需授予 **RECORD_AUDIO** 权限
4. 重试命令

---

### 坑7：canvas 命令失败

**问题表现**：canvas.eval / canvas.snapshot / canvas.navigate 失败

**官方错误码**：`NODE_BACKGROUND_UNAVAILABLE`

**原因**：
1. App 在后台
2. 主机未创建 canvas 目录

**解决**：
1. 确保 App 在**前台**
2. 在主机创建：
   ```bash
   mkdir -p ~/.openclaw/workspace/canvas
   echo "<h1>Hello</h1>" > ~/.openclaw/workspace/canvas/index.html
   ```

---

### 坑8：location.get 失败

**问题表现**：获取位置失败

**官方错误码**：`LOCATION_PERMISSION_REQUIRED`、`LOCATION_DISABLED`

**解决**：
1. 平板设置中开启位置权限
2. OpenClaw App 设置中启用 **Location**

---

### 坑9：消息发不出去

**问题表现**：发送消息无响应

**检查**：
1. Gateway 运行中？
2. 网络通？

**测试**：
```bash
ping 192.168.x.x
```

---

### 坑10：screen.record 失败

**问题表现**：屏幕录制失败

**原因**：缺少屏幕录制权限

**解决**：
1. 在平板设置中授予屏幕录制权限
2. 确保 App 在**前台**

---

## 二、官方故障排除命令

### 2.1 常用诊断命令

```bash
# Gateway 状态
openclaw status
openclaw gateway status

# 节点状态
openclaw nodes status
openclaw nodes describe --node <node-id>

# 设备列表
openclaw devices list

# 查看日志
openclaw logs --follow

# 诊断
openclaw doctor
```

### 2.2 节点命令 ladder

```bash
openclaw status
openclaw gateway status
openclaw logs --follow
openclaw doctor
openclaw channels status --probe

# Node specific checks
openclaw nodes status
openclaw nodes describe --node <idOrNameOrIp>
openclaw approvals get --node <idOrNameOrIp>
```

---

## 三、常见错误码速查

| 错误码 | 说明 | 解决 |
|--------|------|------|
| `NODE_BACKGROUND_UNAVAILABLE` | App 在后台 | 切换到前台 |
| `CAMERA_PERMISSION_REQUIRED` | 缺少相机权限 | 授予 CAMERA 权限 |
| `RECORD_AUDIO_PERMISSION_REQUIRED` | 缺少录音权限 | 授予 RECORD_AUDIO 权限 |
| `LOCATION_PERMISSION_REQUIRED` | 缺少位置权限 | 授予位置权限 |
| `LOCATION_DISABLED` | 位置功能关闭 | 开启位置功能 |
| `LOCATION_BACKGROUND_UNAVAILABLE` | 只有"使用时"权限但App在后台 | 切换到前台或授予"始终"权限 |
| `SYSTEM_RUN_DENIED` | 执行被拒绝 | 检查 exec approvals |
| `SYSTEM_RUN_DENIED: allowlist miss` | 命令被 allowlist 模式阻止 | 添加到 allowlist |

---

## 四、前置要求

以下功能**必须** App 在前台：

- `canvas.*` 命令
- `camera.*` 命令
- `screen.*` 命令

---

## 五、安装经验总结

### 经验1：手动连接更可靠

自动发现有时不稳定，建议使用手动输入：
- Host: `192.168.x.x`（主机局域网 IP）
- Port: `18789`

### 经验2：先测试局域网再考虑 Tailscale

先用同一 WiFi 测试连接，成功后再配置 Tailscale 远程连接。

### 经验3：配对后会自动重连

首次配对成功后，App 重启会自动连接 Gateway。

### 经验4：前台要求

canvas.* / camera.* / screen.* 命令必须 App 在前台。

### 经验5：权限需要手动授予

部分权限（如相机、位置）需要在平板设置中手动授予，不能只靠运行时请求。

---

## 六、完整安装流程

1. **下载 APK** → GitHub Releases 或 Play Store
2. **平板设置** → 开发者选项、USB 调试、未知来源安装
3. **主机配置** → 启动 Gateway、获取 IP
4. **App 连接** → 安装打开、选择连接方式
5. **配对批准** → 主机执行 approve 命令
6. **验证使用** → 测试 Chat/Voice/Camera/Canvas

---

## 七、Tailscale 远程连接流程

1. 安装 Tailscale（主机 + 平板）
2. 登录同一账号
3. 主机配置：`openclaw configure set gateway.bind tailnet`
4. 重启 Gateway
5. 获取 Tailscale IP：`tailscale ip -4`
6. 平板连接时使用 Tailscale IP
