<div align="center">

# 📷 UVCcam-

**在 Android 上同时接入多路 USB 摄像头的预览与录制方案**

基于 [saki4510t/UVCCamera](https://github.com/saki4510t/UVCCamera) 二次开发
完善了 Android 11 的权限适配，并把应用层扩展为 **6 路摄像头**独立预览与录制

![Platform](https://img.shields.io/badge/platform-Android-3DDC84?logo=android&logoColor=white)
![Language](https://img.shields.io/badge/language-C%20%2F%20Java-orange)
![Min SDK](https://img.shields.io/badge/minSdk-18%20(4.3)-blue)
![License](https://img.shields.io/badge/license-Apache%202.0-blue)
![USB](https://img.shields.io/badge/interface-UVC%20%2F%20OTG-lightgrey)

</div>

---

## ✨ 特性

| | |
|---|---|
| 🎥 **六路并发** | 界面划分为 6 个独立窗口，最多同时接入 6 路 UVC 摄像头，各自独立开关预览 |
| 🔴 **独立录制** | 每一路可单独启停录制，互不干扰，采用 MediaCodec H.264 硬编码 |
| 🔌 **即插即用** | 插入 USB 摄像头后弹出设备选择对话框，选中即可出图，无需重启应用 |
| 🔐 **Android 11 适配** | 补齐了 Android 11 上的存储权限申请链路 |
| 🚫 **免 Root** | 基于 Android USB Host API，不需要 root 权限 |

---

## 🛠 在原版基础上做的改造

1. **应用层扩展为六路** —— 布局与 `MainActivity` 均按 6 个独立通道组织（`camera_view_first` ~ `camera_view_sixth`），每个通道持有自己的 `UVCCameraHandler`，预览、打开/关闭、录制全部相互独立。

2. **Android 11 存储权限适配** —— 新版系统收紧了外部存储访问，本项目在 `AndroidManifest.xml` 中补充声明了 `MANAGE_EXTERNAL_STORAGE`（Android 11 引入的"所有文件访问权限"），并在每次启动录制前串联校验 `WRITE_EXTERNAL_STORAGE` 与 `RECORD_AUDIO`，权限未授予时引导申请，避免录制静默失败。

3. **依赖仓库换为国内镜像** —— 原版依赖的 `raw.github.com` 仓库在国内常拉取失败，已替换为阿里云 Maven 与 Gitee 上的 `libcommon` 镜像，同步即可构建。

---

## 📦 环境要求

| 项目 | 版本 |
|---|---|
| Android Gradle Plugin | 3.1.4 |
| compileSdkVersion / targetSdkVersion | 27 |
| buildToolsVersion | 27.0.3 |
| minSdkVersion | 18（Android 4.3） |
| Support Library | 27.1.1 |
| 构建产物 ABI | `armeabi-v7a` |

> ⚠️ 设备必须支持 **USB Host（OTG）** 模式，并准备一根 OTG 转接线。

---

## 🚀 构建与运行

```bash
git clone https://github.com/kghggt/UVCcam-.git
```

1. 用 Android Studio 打开项目根目录，**暂时跳过 Gradle / AGP 的升级提示**（本项目锁在 AGP 3.1.4）。
2. 等待 Gradle 同步完成。依赖走阿里云与 Gitee 镜像，一般无需代理。
3. 用数据线连接设备（开启 USB 调试），运行 `app` 模块。

---

## 📁 项目结构

| 目录 | 说明 |
|---|---|
| `app/` | 应用层：六路摄像头的界面布局与交互逻辑（`MainActivity`） |
| `libuvccamera/` | UVC 核心库。JNI native 层含 `libusb`、`libuvc`、`libjpeg-turbo`、`rapidjson`，Java 层是对 native 的封装 |
| `usbCameraCommon/` | 通用组件：相机 Handler、预览控件（`UVCCameraTextureView`）、MediaCodec 录制封装（`MediaVideoEncoder`） |

---

## 🎮 使用说明

1. 用 OTG 线把 UVC 摄像头接到手机或平板。
2. 首次启动会依次请求相机、存储和录音权限 —— 请全部允许。
3. 点击任意一个预览窗口，弹出设备选择框，选择要绑定的摄像头即可出图。
4. 点击该窗口下方的录制按钮开始录制（按钮变红），再次点击停止。
5. 视频文件保存在外部存储目录下。

> **Android 11 及以上注意**：由于声明了 `MANAGE_EXTERNAL_STORAGE`，系统不会自动授予，需要手动到  
> `设置 → 应用 → 本应用 → 权限 → 所有文件访问权限` 开启一次，否则录制可能无写入权限。

---

## 📸 效果预览

> 建议在此处放置一张真机运行截图（六路预览界面）或一段操作 GIF。

---

## ⚠️ 已知限制

- 仅构建 `armeabi-v7a` 一种 ABI（`build.gradle` 中的 `abiFilter`），如需 64 位可自行添加 `arm64-v8a` 并重新编译 native 库。
- `targetSdkVersion` 为 27，在更高版本系统上部分存储行为受限，这也是需要 `MANAGE_EXTERNAL_STORAGE` 的原因。
- 仅支持标准 **UVC 协议**摄像头，非 UVC 设备无法识别。
- 实际录制分辨率取决于摄像头本身支持的 UVC 格式。

---

## 🙏 致谢

本项目基于 [saki4510t/UVCCamera](https://github.com/saki4510t/UVCCamera)（Apache License 2.0）。  
native 层使用了 [libusb](https://libusb.info/)、[libuvc](https://github.com/libuvc/libuvc)、[libjpeg-turbo](https://github.com/libjpeg-turbo/libjpeg-turbo) 与 [RapidJSON](https://github.com/Tencent/rapidjson)。

## 📄 许可证

沿用上游的 **Apache License 2.0**，详见上游仓库。
