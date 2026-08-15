# Acer TravelMate TX520 Hackintosh EFI

> **⚠️ 本 EFI 仅适用于 macOS 14.8.5 版本**
>
> 经过四个月反复测试，i5-8250U + UHD620 这个配置在 14.8.5 下流畅度最高、最稳定。**如果你也是这个配置，只推荐装这个版本。**

**🌟 这是全网首份针对LVDS接口屏幕闪屏问题的公开完整修复方案，专为Acer TravelMate TX520 (i5-8250U/UHD620) 打造，并已整合所有必要驱动，可实现开箱即用。**

![macOS](https://img.shields.io/badge/macOS-14.8.5-red)
![OpenCore](https://img.shields.io/badge/OpenCore-0.8.8-green)
![CPU](https://img.shields.io/badge/CPU-i5--8250U-blue)
![WiFi](https://img.shields.io/badge/WiFi-Intel%20AC%207265-orange)
![Status](https://img.shields.io/badge/Status-Stable-brightgreen)
![License](https://img.shields.io/badge/License-MIT-yellow)

---

## 硬件配置

| 项目 | 规格 |
|------|------|
| 型号 | Acer TravelMate TX520 |
| CPU | Intel Core i5-8250U |
| 核显 | Intel UHD Graphics 620 |
| 屏幕 | 1366×768（LVDS接口） |
| 内存 | 16GB DDR4（已升级） |
| 硬盘 | 2TB SSD（已升级） |
| 无线网卡 | Intel AC 7265 |
| OpenCore 版本 | 0.8.8 |

---

## ✨ 特色亮点

- **LVDS 闪屏修复**：这款机型用的是 LVDS 接口的屏幕，很多人在黑苹果下闪屏无解，本 EFI 已完美修复。
- **WiFi 断流修复**：Intel AC 7265 网卡在 macOS 下容易断流，通过特定 USB 电源管理驱动间接稳定了网卡供电，已稳定不掉线。
- **触摸板双协议切换**：本机型触摸板同时支持 I2C 和 PS/2 两种协议，需关闭 I2C、仅保留 PS/2 方可正常工作（已配置好）。

---

## 功能状态

| 功能 | 状态 |
|------|------|
| 核显 / 屏幕显示 | ✅ 正常 |
| WiFi | ✅ 正常 |
| 蓝牙 | ✅ 正常 |
| 触摸板 / 键盘 | ✅ 正常 |
| 声卡 | ✅ 正常 |
| USB 接口 | ✅ 正常 |
| 合盖关屏 | ✅ 正常 |
| Sidecar 随航 | ✅ 正常 |
| 手动睡眠唤醒 | ✅ 正常 |
| **自动睡眠唤醒** | ❌ 未攻克（手动睡眠正常，自动睡眠无法唤醒） |

---

## BIOS 设置（非常重要！）

很多网友下载了 EFI 却连 OpenCore 界面都看不到，99% 是 BIOS 没调对。**替换 EFI 之前，请先检查 BIOS 设置。**

### 必须关闭
- **Secure Boot**（安全启动）
- **TPM 2.0**（或 PTT、Intel PTT）
- **VT-d**（如果 BIOS 里有的话）

### 必须开启
- **AHCI 模式**（硬盘模式）
- **UEFI 引导**（不是 Legacy）
- **VT-x**（虚拟化技术）

### 怎么进 BIOS？
开机时按 **F2** 或 **Del**（Acer 一般是 F2）。如果不会调，去搜索 **"Acer TX520 关闭 Secure Boot"**。

---

## 安装教程

### 从零安装（没有 macOS 的看这里）

1. 下载 macOS 14.8.5 恢复版镜像.rdr（推荐去**黑果小兵**的网站下载）
2. 用 R-Drive Image 将镜像写入空闲分区80-60GB
3. 用 DiskGenius 挂载 EFI 分区，替换为本仓库提供的 EFI 文件夹
4. 用 Bootice 将 OpenCore 添加到 Windows 启动菜单并设为第一启动项（可选）
5. 重启，选择 macOS，系统就完成了

> 详细图文教程请查看 README 完整版。

### 已有 macOS（只换 EFI）

1. 挂载 EFI 分区
2. 备份原 EFI，替换为本仓库的 EFI
3. 重启进入 OpenCore 引导界面

---

## EFI 文件结构

```
EFI/
├── OC/
│   ├── ACPI/
│   │   ├── SSDT-PLUG.aml
│   │   └── SSDT-EC.aml
│   ├── Drivers/
│   │   ├── OpenRuntime.efi
│   │   └── HfsPlus.efi
│   ├── Kexts/
│   │   ├── Lilu.kext
│   │   ├── WhateverGreen.kext
│   │   ├── AirportItlwm.kext
│   │   └── ...
│   └── config.plist
└── BOOT/
    └── BOOTx64.efi
```

---

## 关于提问和反馈

遇到问题想报错，**必须先开启跑代码模式（在config中搜索boot-args 中添加 `-v`），把最后几行报错截图发给我**，否则我无法判断问题。

**我会认真看的情况：**
- 卡代码了，**并且贴了开启跑代码模式后的报错截图**
- 清楚地说明你的操作步骤、电脑配置、出了什么现象

**我看了也不会回的情况：**
- 一句"我卡住了"，然后什么都没了
- 问"到底能不能用"—— README 写的很清楚
- 没看 README 就问怎么用

**如果问出那种但凡动一下脑子都不会问的问题，我只会回复一句：**"建议买个Mac mini，黑苹果不适合你。"

---

## 开源许可证

本项目采用 **MIT License**，你可以自由使用、修改、分发，但需要保留原作者版权声明。如果更改导致的报错，卡死本人概不负责

---

## 致谢

感谢 OpenCore 团队、WhateverGreen 团队以及所有 Kext 开发者，没有他们的开源项目，黑苹果不会如此精彩。

感谢 **黑果小兵** 提供的资源与教程，为无数黑苹果玩家指明了方向。

感谢所有在 GitHub 上无私分享自己 EFI 的前辈，你们的代码和经验让我少走了无数弯路。

最后，感谢愿意尝试本 EFI 的你。希望它能帮你省下几天甚至几周的折腾时间，让你真正享受这台机器在 macOS 下的体验。

如果你觉得有用，欢迎点个 ⭐ 支持一下；如果遇到了问题，也欢迎提 Issue，但请记得带上跑码截图。

**Happy Hacking!** 🍻
