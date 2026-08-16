# 🚀 黑苹果屏幕闪屏 · 通用底层终极修复方案（通用于所有LVDS/eDP机型）

> **这不是一篇只针对某款电脑的教程，而是一套通杀的底层闪屏修复逻辑。**
>
> 本方法 **不限制 macOS 版本**（Monterey/Ventura/Sonoma/Sequoia 全系通用）、**不限制主板品牌**、**不限制 CPU 代数**。只要你的笔记本是 **UHD 620 / UHD 630 及同架构核显 + LVDS 或 eDP 接口屏幕**，并且遇到了开机闪屏、间歇性抖动、睡眠唤醒后闪烁、滚动页面忽明忽暗等问题，这套参数可以直接拿去用。
>
> 📌 **本仓库提供两部分内容，请按需取用：**
> 1. **🎯 通用闪屏修复参数**（核心内容，所有人必看）
> 2. **📁 Acer TX520 参考 EFI**（附赠案例，仅限同款机型参考）

![Status](https://img.shields.io/badge/Status-Stable-brightgreen)
![License](https://img.shields.io/badge/License-MIT-yellow)
![macOS](https://img.shields.io/badge/macOS-全版本支持-blue)
![GPU](https://img.shields.io/badge/GPU-UHD%20620%2F630%20通用-orange)

---

## 📦 仓库结构说明

| 内容 | 定位 | 适用人群 |
|------|------|---------|
| **🎯 通用闪屏修复参数** | **主内容（核心）** | 所有遇到闪屏的黑苹果笔记本用户 |
| **📁 Acer TravelMate TX520 EFI** | **附赠参考案例** | 仅限 `i5-8250U + UHD 620 + macOS 14.8.5` 同款机型用户 |

---

## 🎯 一、核心通用修复方案（所有人直接使用）

### ✅ 适用条件（只要满足以下任意一条，此法就有效）
- 核显为 **UHD 620** 或 **UHD 630**
- 屏幕接口为 **LVDS**（老款笔记本常见）或 **eDP**（新款笔记本常见）
- 症状：开机闪 Logo、进系统后间歇性横纹抖动、睡眠唤醒后疯狂闪烁、浏览浅色背景时屏幕忽明忽暗

### 📝 操作步骤（仅需两步，五分钟搞定）

#### 第 1 步：在 config.plist 中添加显卡属性

找到 `DeviceProperties` -> `Add` 下面，**在你的核显设备路径（通常是 `PciRoot(0x0)/Pci(0x2,0x0)`）下**，添加如下键值：

```xml
<key>DeviceProperties</key>
<dict>
    <key>Add</key>
    <dict>
        <key>PciRoot(0x0)/Pci(0x2,0x0)</key>
        <dict>
            <key>framebuffer-con0-flags</key>
            <data>hwEAAA==</data>
            <!-- 
                如果 con0 无效，尝试改为 con1 或 con2：
                <key>framebuffer-con1-flags</key>
                <data>hwEAAA==</data>
            -->
        </dict>
    </dict>
</dict>
💡 如果闪屏依旧，把 con0 换成 con1 或 con2 再试，这是因为不同主板的屏幕输出通道索引不同。

第 2 步：在 boot-args 中添加启动参数
在 NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82 下的 boot-args 字符串中，追加以下两个参数：

bash
-igfxcdc igfxonln=1
第 3 步：重启生效
保存 config.plist，替换 EFI 后重启电脑。如果不出意外，闪屏应该彻底消失。

🔬 原理简述（给喜欢深究的玩家看）
framebuffer-conX-flags = 0x00000187：这个数值是 Intel 显卡驱动中一个隐藏的电源管理标志位。它的作用是强制锁定 LVDS/eDP 通道的供电稳态，禁止 macOS 的 GPU 驱动在空闲时自动降低屏幕接口电压，从而彻底消除因电压波动引发的像素抖动。

-igfxcdc + igfxonln=1：禁用 WhateverGreen 的时钟动态调整机制，强制保持显示管线始终在线，避免屏幕进入“半休眠”状态时产生的闪烁。

此方法的核心优势在于： 它绕过了复杂的 EDID 注入、BusID 修正、通道数计算等门槛，直接作用于核显的电源管理单元（PMU）。因此，它不挑机型、不挑 BIOS 版本、不挑 macOS 小版本号，是一种“一劳永逸”的底层通解。

📁 二、附赠参考 EFI（仅限 Acer TravelMate TX520 同款机型）
⚠️ 以下内容为附赠的实战案例，仅适用于以下固定硬件配置：

型号：Acer TravelMate TX520

CPU：Intel Core i5-8250U

核显：Intel UHD Graphics 620

屏幕：1366×768（LVDS 接口）

系统版本：macOS 14.8.5（已通过四个月反复测试，此版本在此机型上流畅度最高、最稳定）

如果你不是这个配置，请忽略本部分，只提取上面第一部分的通用参数即可。

🖥️ 硬件配置详情
项目	规格
型号	Acer TravelMate TX520
CPU	Intel Core i5-8250U
核显	Intel UHD Graphics 620
屏幕	1366×768（LVDS 接口）
内存	16GB DDR4（已升级）
硬盘	2TB SSD（已升级）
无线网卡	Intel AC 7265
OpenCore 版本	0.8.8
✨ 此 EFI 包含的特色修复（仅供参考）
LVDS 闪屏修复：已集成上述通用方案，完美解决。

WiFi 断流修复：Intel AC 7265 网卡在 macOS 下容易断流，通过特定 USB 电源管理驱动间接稳定了网卡供电，已稳定不掉线。

触摸板双协议切换：本机型触摸板同时支持 I2C 和 PS/2 两种协议，需关闭 I2C、仅保留 PS/2 方可正常工作（已配置好）。

启动应急提示：如果启动时提示“出现问题需要重启”，不要犹豫直接重启，重启完后问题应该会解决。

📊 功能状态表
功能	状态
核显 / 屏幕显示	✅ 正常
WiFi	✅ 正常
蓝牙	✅ 正常
触摸板 / 键盘	✅ 正常
声卡	✅ 正常
USB 接口	✅ 正常
合盖关屏	✅ 正常
Sidecar 随航	✅ 正常
手动睡眠唤醒	✅ 正常
自动睡眠唤醒	❌ 未攻克（手动正常，自动无法唤醒）
⚙️ BIOS 设置（非常重要！仅限 TX520 用户）
很多网友下载了 EFI 却连 OpenCore 界面都看不到，99% 是 BIOS 没调对。替换 EFI 之前，请先检查 BIOS 设置。

必须关闭：

Secure Boot（安全启动）

TPM 2.0（或 PTT、Intel PTT）

VT-d（如果 BIOS 里有的话）

必须开启：

AHCI 模式（硬盘模式）

UEFI 引导（不是 Legacy）

VT-x（虚拟化技术）

怎么进 BIOS？ 开机时按 F2 或 Del（Acer 一般是 F2）。如果不会调，去搜索 "Acer TX520 关闭 Secure Boot"。

📥 安装教程（仅限参考 EFI 使用）
从零安装（没有 macOS 的情况）
下载 macOS 14.8.5 恢复版镜像 .rdr（推荐去 黑果小兵 的网站下载）

用 R-Drive Image 将镜像写入空闲分区（建议 80-160GB）

用 DiskGenius 挂载 EFI 分区，替换为本仓库提供的 EFI 文件夹

用 Bootice 将 OpenCore 添加到 Windows 启动菜单并设为第一启动项（可选）

重启，选择 macOS，系统将自动完成安装

已有 macOS（只换 EFI）
挂载 EFI 分区

备份原 EFI，替换为本仓库的 EFI 文件夹

重启进入 OpenCore 引导界面

📂 EFI 文件结构（参考）
text
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
❓ 三、遇到问题怎么反馈？（必读）
想报错或提问，必须先开启跑代码模式（在 config 中搜索 boot-args，添加 -v），把最后几行报错截图发给我，否则我无法判断问题根源。

我会认真看的情况：

卡代码了，并且贴了开启跑代码模式后的报错截图

清楚地说明你的操作步骤、电脑配置、出了什么现象

我看了也不会回的情况：

一句“我卡住了”，然后什么都没了

问“到底能不能用”——README 已经写得很清楚

没看 README 就问怎么用

🗣️ 特别提醒： 如果问出那种但凡动一下脑子都不会问的问题，我只会回复一句：“建议买个 Mac mini，黑苹果不适合你。”

📄 四、开源许可证
本项目采用 MIT License，你可以自由使用、修改、分发，但需要保留原作者版权声明。因自行修改导致的任何报错、卡死甚至硬件损坏，本人概不负责。

🙏 五、致谢
感谢 OpenCore 团队、WhateverGreen 团队 以及所有 Kext 开源开发者。

感谢 黑果小兵 提供的资源与教程，为无数黑苹果玩家指明了方向。

感谢所有在 GitHub 上无私分享自己 EFI 的前辈，你们的代码和经验让我少走了无数弯路。

特别感谢愿意尝试本方案的你。希望它能帮你省下几天甚至几周的折腾时间，让你真正享受这台机器在 macOS 下的体验。

如果你觉得有用，欢迎点个 ⭐ Star 支持一下；如果遇到了问题，也欢迎提 Issue，但请记得带上跑码截图。

Happy Hacking! 🍻
