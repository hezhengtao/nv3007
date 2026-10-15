# NV3007 桌面伴侣 — 更新发布

NV3007 智能桌面屏（ESP32-S3 + 2.79 寸 428×142 长条屏）的固件与 PC 上位机更新发布仓库。
源码私有，本仓库仅承载更新分发。

## 更新通道区分

上位机「固件更新」对话框拉取 Releases 列表，按 **tag 前缀** 区分通道，各取最新一条：

| 通道 | Release Tag | 附件命名 | 用途 |
|---|---|---|---|
| 固件 | `fw-vX.Y.Z` | `nv3007-fw-vX.Y.Z.bin` | ESP32 固件，上位机 OTA 推送到设备 |
| 上位机 | `pc-vX.Y.Z` | `nv3007-pc-vX.Y.Z.zip` | PC 端程序，下载后解压手动替换 |

两个通道版本号独立演进，一次发布可以只发其中一个。

## 固件更新链路（设备端已支持）

```
上位机点「在线检查更新」→ GitHub Releases 查 fw- 通道最新版
  → 与设备当前版本对比, 有新版自动下载 bin
  → 点「发送到设备」→ USB 串口帧推送:
       0x10 OTA_START {"size":N}  → esp_ota_begin()
       0x11 OTA_DATA  512B/片 ×N  → esp_ota_write()
       0x12 OTA_DONE              → esp_ota_end() + 切换启动分区 + 重启
```

设备端要求（已具备）：分区表含 `otadata` + `ota_0`/`ota_1` 双槽位，固件处理 0x10-0x12 帧。

## 发布步骤

### 固件
1. ESP-IDF 编译得到固件 bin
2. 改名 `nv3007-fw-vX.Y.Z.bin`
3. GitHub → Releases → New release → tag 填 `fw-vX.Y.Z` → 上传 bin → Publish

### 上位机
1. `build_exe.bat` 打包（PyInstaller）
2. 产物压缩为 `nv3007-pc-vX.Y.Z.zip`
3. 新建 tag `pc-vX.Y.Z` 的 Release 上传 zip → Publish
