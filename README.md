<div align="center">
  <a href="#cn">中文</a> | 
  <a href="#en">English</a>
</div>

---

<!-- 中文部分开始 -->
<span id="cn"></span>

# 🖼️ 透明截图 (Transparent Screenshot)

*基于 LSPosed 框架的 Xposed 模块，为应用提供全面的截图、录屏及屏幕投射防护。*

[![Android](https://img.shields.io/badge/Android-10%2B-brightgreen)]()
[![API](https://img.shields.io/badge/Xposed%20API-102-blue)]()
[![License](https://img.shields.io/badge/License-MIT-yellow)]()

---

## ✨ 核心功能

### 🛡️ 应用层功能

| 功能 | 说明 |
|------|------|
| **启用默认窗口隐藏方案** | 通过 `setSkipScreenshot` 标志阻止系统截图和录屏。 |
| **强制允许截屏** | 移除应用自身的 `FLAG_SECURE` 与 `skipScreenshot` 保护，使其窗口始终可被截取（与隐藏功能方向相反，用于解除应用自带的截屏限制）。 |
| **去除阴影遮罩** | 移除窗口的阴影效果。 |
| **窗口不接收触摸与焦点** | 设置 `FLAG_NOT_FOCUSABLE \| FLAG_NOT_TOUCHABLE \| FLAG_NOT_TOUCH_MODAL`，窗口无法接收任何触摸和焦点事件。**(⚠️ 注意：窗口将无法点击。)** |
| **绕过焦点检查** | 仅设置 `FLAG_NOT_FOCUSABLE`，保留触摸能力，但输入法（键盘）不会弹出。**(⚠️ 注意：键盘输入将无法弹出。)** |
| **隐藏最近任务界面** | 从最近任务列表中移除应用卡片。 |
| **修改窗口标题为** | 将窗口标题伪装为系统录屏组件，支持全局品牌选择或自定义标题。 |
| **允许截屏至壁纸层** | 窗口完全透明时显示壁纸而非黑色（仅部分应用生效）。 |

### 🔧 系统层隐藏

系统层隐藏通过注入系统框架，在系统服务层级直接拦截窗口的渲染，可同时隐藏窗口及其所属 Task 的阴影/边框。此功能需要在设置中手动开启，并在主界面点击 **系统框架** 进行配置。

> ⚠️ 开启后可能略有卡顿，请按需使用。

### 🧩 模块管理（2.1.2 新增）

- **动态作用域**：可直接在模块内添加 / 移除作用域，动态向框架申请，无需再打开 LSPosed 管理器逐个勾选。
- **启用 Hook 开关**：每个应用的配置页都有总开关，关闭后该应用立即停止 Hook，无需移除作用域即可临时停用。
- **热重载**：基于 libxposed 102，全面适配热重载，在模块里更改功能配置后立即生效，无需重启目标应用。
- **全新界面**：界面风格升级为 Material 3，并适配动态取色。

---

## 📖 使用方法

1. 确保设备已 **Root** 并安装了 **LSPosed** 框架。
2. 安装本模块。
3. 打开 LSPosed 管理器，启用本模块。
4. 在模块界面中 **勾选需要生效的目标应用**（作用域），也可以直接在模块内通过添加按钮动态申请作用域。
5. 点击目标应用进入配置页，开启所需功能。
6. 配置更改后通过热重载 **立即生效**，无需强制停止或重启目标应用。

### 🌐 全局标题设置

在模块主界面右上角菜单 → **设置**，可以选择默认的窗口标题品牌。应用配置中选择 **“全局”** 模式时，将自动跟随此设置。

### 🔌 系统层隐藏设置

1. 进入 **设置** → 开启 **系统层隐藏** 开关。
2. 系统将请求添加 `system` 到模块作用域。
3. 返回主界面，点击 **系统框架** 条目。
4. 在系统层隐藏界面中，选择需要隐藏的应用（支持搜索）。
5. 已开启隐藏的应用会自动排在列表最上方。
6. 右上角菜单可切换显示/隐藏系统应用。

---

## 🔬 技术说明

### SkipScreenshot 原理

`SkipScreenshot` 是系统窗口与渲染流程中的标识，用于指示某个窗口或界面在进行截图、录屏或屏幕投射时应被 **跳过**，不参与最终画面的合成。

**当该标识生效时：**
- 应用界面在设备本地仍然可见、可交互。
- 系统截图、录屏或投射结果中 **不会包含** 该界面内容。

### SystemUI 增强 - Oplus 小窗菜单隐藏

在 **设置** 中开启 **SystemUI 菜单隐藏（Oplus 增强）** 后，模块会 Hook SystemUI 进程中的 `FlexibleMenuManager`，在 OPPO/一加设备的自由窗口（小窗）弹出菜单时，自动对该菜单的 Surface 施加 `setSkipScreenshot`，使其不显示在截图/录屏中。

此功能仅适用于 OPPO/一加 ColorOS 15/16 设备。

### 修改窗口标题原理

当窗口标题为特定字符串时，系统在截屏和录屏中会跳过合成该窗口，从而达到隐藏效果。

### 系统层隐藏原理

系统层隐藏直接作用于系统服务 (`system_server`) 的窗口管理机制，在目标窗口的 Surface 创建时就为其设置 `setSkipScreenshot` 标志，并将该标志同步应用到窗口所属的 Task 容器，从而同时隐藏窗口内容和阴影边框。

---

## ⚠️ 注意事项

> **本模块涉及对系统窗口管理机制的深度干预。使用前请确保您了解相关风险，并具备基本的窗口系统知识。因不当配置导致的系统界面异常、功能失效或其他不可预期的问题，由用户自行承担。**

---

## 🔒 权限说明

本模块需要以下权限：

- `QUERY_ALL_PACKAGES` – 用于获取已安装应用列表。
- Xposed 框架权限 – 用于 Hook 目标应用窗口。

---

## 📄 免责声明

本工具仅供 **安全研究** 和 **个人学习** 使用。  
请勿用于侵犯他人隐私或版权的用途。  
使用本模块产生的任何后果由用户自行承担。

---

## 🙏 致谢

本模块的核心功能实现受到了以下优秀项目的启发：

| 项目 | 说明 |
|------|------|
| [SpoofTitle](https://github.com/ZhongYeZi-meow/SpoofTitle) | 窗口标题伪装逻辑 |
| [-HideScreen-](https://github.com/zjkkzk/-HideScreen) | 防截屏问题修复 |

---

<div style="text-align: center;">

**Transparent Screenshot** © 2025 · Made with ❤️

</div>

---

<!-- 英文部分开始 -->
<span id="en"></span>

# 🖼️ Transparent Screenshot

*A LSPosed module that provides comprehensive screenshot, screen recording and screen casting protection for apps.*

[![Android](https://img.shields.io/badge/Android-10%2B-brightgreen)]()
[![API](https://img.shields.io/badge/Xposed%20API-102-blue)]()
[![License](https://img.shields.io/badge/License-MIT-yellow)]()

---

## ✨ Core Features

### 🛡️ App-Level Features

| Feature | Description |
|---------|-------------|
| **Enable default window hiding** | Prevents system screenshots and recordings via the `setSkipScreenshot` flag. |
| **Force allow screenshots** | Removes the app's own `FLAG_SECURE` and `skipScreenshot` protection so its windows are always capturable (the opposite of hiding; useful for lifting an app's built-in screenshot restrictions). |
| **Remove shadow mask** | Removes window shadow effects. |
| **Block touch and focus** | Sets `FLAG_NOT_FOCUSABLE \| FLAG_NOT_TOUCHABLE \| FLAG_NOT_TOUCH_MODAL` – window becomes unclickable. **(⚠️ Note: the window will not respond to any touch or focus events.)** |
| **Bypass focus check** | Sets only `FLAG_NOT_FOCUSABLE` – touch events remain intact, but the input method (keyboard) will not pop up. **(⚠️ Note: keyboard input will not appear.)** |
| **Hide recent tasks** | Removes the app card from the recent tasks list. |
| **Modify window title to** | Disguises the window title as a system recorder component; supports global brand selection or custom title. |
| **Allow screenshot to wallpaper layer** | Shows wallpaper instead of black when the window is fully transparent (works with some apps only). |

### 🔧 System-Level Hiding

System‑level hiding injects into the system framework to intercept window rendering at the system service level. It can hide both the window itself and the shadows/borders of its parent Task. This feature must be manually enabled in Settings, and configured by tapping **System Framework** on the main screen.

> ⚠️ May cause slight lag; enable only when necessary.

### 🧩 Module Management (new in 2.1.2)

- **Dynamic scope**: Add or remove scope entries right inside the module; scope is requested from the framework dynamically, no need to tick apps in the LSPosed manager one by one.
- **Enable Hook switch**: Each app's config page has a master switch; turning it off stops hooking for that app immediately, without removing it from scope.
- **Hot reload**: Fully adapted to libxposed 102 hot reload; feature changes take effect immediately, with no need to restart the target app.
- **Refreshed UI**: The interface has been upgraded to Material 3 with dynamic color support.

---

## 📖 Usage

1. Ensure your device is **rooted** and has **LSPosed** framework installed.
2. Install this module.
3. Open LSPosed manager, enable the module.
4. Select the **target apps** (scope) that you want the module to affect; you can also request scope dynamically from within the module via the add button.
5. Tap a target app to open its configuration page, and enable the desired features.
6. Configuration changes take effect **immediately** via hot reload; no need to force stop or restart the target app.

### 🌐 Global Title Setting

In the top‑right menu of the main screen → **Settings**, you can choose a default window title brand. When an app’s title mode is set to **"Global"**, it will automatically follow this setting.

### 🔌 System-Level Hiding Setup

1. Go to **Settings** → turn on **System‑level hiding**.
2. The system will request to add `system` to the module scope.
3. Return to the main screen and tap the **System Framework** entry.
4. In the system‑hide screen, select the apps you wish to hide (search supported).
5. Enabled apps are automatically sorted to the top of the list.
6. Use the top‑right menu to toggle showing/hiding system apps.

---

## 🔬 Technical Notes

### How `SkipScreenshot` Works

`SkipScreenshot` is a flag in the system window and rendering pipeline that marks a window or surface to be **skipped** during screenshot, recording, or casting operations.

**When this flag is active:**
- The app UI remains visible and interactive on the local device.
- The window content **will not be included** in system screenshots, recordings, or casts.

### SystemUI Enhancement – Oplus Floating Menu Hiding

When **SystemUI menu hiding (Oplus)** is enabled in **Settings**, the module hooks into `FlexibleMenuManager` in the SystemUI process. On OPPO/OnePlus devices, when the floating window menu appears, it automatically applies `setSkipScreenshot` to the menu’s Surface, hiding it from screenshots/recordings.

This feature is only available on OPPO/OnePlus devices running ColorOS 15/16.

### How Window Title Modification Works

When the window title matches certain well‑known system recorder component strings, the system skips compositing that window during screen capture, effectively hiding it.

### How System‑Level Hiding Works

System‑level hiding operates directly on the window management mechanism of the system service (`system_server`). It sets the `setSkipScreenshot` flag on the target window’s Surface at creation time, and also applies the same flag to the window’s parent Task container, thereby hiding both the window content and its shadow/border.

---

## ⚠️ Cautions

> **This module deeply intervenes in the system window management mechanism. Please ensure you understand the risks and possess basic knowledge of window systems before use. Any system UI abnormalities, feature failures, or other unforeseen issues caused by improper configuration are the user's own responsibility.**

---

## 🔒 Permissions

This module requires the following permissions:

- `QUERY_ALL_PACKAGES` – to retrieve the list of installed apps.
- Xposed framework privileges – to hook into target app windows.

---

## 📄 Disclaimer

This tool is intended for **security research** and **personal learning** only.  
Do not use it for infringing upon others’ privacy or copyright.  
The user assumes all responsibility for any consequences arising from the use of this module.

---

## 🙏 Acknowledgements

Core functionality inspired by the following excellent projects:

| Project | Description |
|---------|-------------|
| [SpoofTitle](https://github.com/ZhongYeZi-meow/SpoofTitle) | Window title spoofing logic |
| [-HideScreen-](https://github.com/zjkkzk/-HideScreen) | Anti‑screenshot fixes |

---

<div style="text-align: center;">

**Transparent Screenshot** © 2025 · Made with ❤️

</div>
