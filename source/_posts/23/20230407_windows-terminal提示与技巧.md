---
title: windows-terminal提示与技巧
date: 2023-02-17 10:49:30
categories:
tags:
  - windows
  - 终端/terminal
---

## 首次启动

首次安装 Windows Terminal时，系统将推送一个 Windows PowerShell 提示符。 默认情况下，Windows Terminal附带 Windows PowerShell、命令提示符和 Azure Cloud Shell 配置文件。

除了这些配置文件之外，如果安装了任何[适用于 Linux 的 Windows 子系统 (WSL)](https://learn.microsoft.com/zh-cn/windows/wsl)发行版，Terminal也会自动为这些发行版创建配置文件。 如果要在计算机上安装其他 WSL 发行版，可以在安装Terminal后进行安装，在下次Terminal启动时，将自动显示这些发行版的配置文件。 这些配置文件将使用 Linux Tux 图像作为其图标。

如果需要，可以更改每个 WSL 分发版的图标。 特定的分发版图标不随附在Terminal内，但可以使用Terminal设置下载和分配。

<!-- more -->

## 默认设置

Windows Terminal附带大量默认设置，包括[配色方案](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/color-schemes)和[键盘快捷键](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/actions)（现在称为“自定义操作”）。 如果想查看默认设置文件，可以按住<kbd> Alt </kbd>并单击下拉菜单中的“设置”按钮。

## 配置

Windows Terminal可以把设置项应用到每个profile，而不必为每个配置文件条目复制设置项。这可以通过在 profiles 对象内的“defaults”数组中添加设置来实现。 详细了解常规[配置文件设置](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/profile-general)、[外观配置文件设置](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/profile-appearance)和[高级配置文件设置](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/profile-advanced)。

``` json
"profiles":
    {
        "defaults":
        {
            // Put settings here that you want to apply to all profiles.
            "fontFace": "Cascadia Code"
        },
        "list":
        []
    }
```

## 重命名选项卡

可以右键单击某个选项卡，然后选择“重命名选项卡”，对该终端会话的一个选项卡进行重命名。 在上下文菜单中单击此选项后，选项卡标题会变为一个文本字段，可以在其中编辑标题。 如果要为每个终端实例设置该配置文件的选项卡标题，可参阅[选项卡标题教程](https://learn.microsoft.com/zh-cn/windows/terminal/tutorials/tab-title)，了解详细信息。

![](https://learn.microsoft.com/zh-cn/windows/terminal/images/tab-rename.gif)

## 选项卡配色

可以右键单击某个选项卡，然后选择颜色，为该终端会话的选项卡配色。 可以从预定义的颜色列表中进行选择，也可以选择自定义...，使用颜色选取器或 RGB/HSV 或 hex 字段来选取任何颜色。

![](https://learn.microsoft.com/zh-cn/windows/terminal/images/tab-color.png)

> 提示
> 
> 使用 hex 字段将选项卡设置为与背景颜色相同的颜色，让外观颜色在整体上显得更一致。

`tabColor` 可以设置为配置文件的一部分。 请参阅[配置文件 - 外观：选项卡颜色](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/profile-appearance#tab-color)。 例如：

``` json
{
  "guid": "{1234abc-abcd-1234-12ab-1234abc}",
  "name": "Windows PowerShell",
  "background": "#012456",
  "tabColor": "#012456",
},
```

`tabColor` 不能设置为配色方案的一部分。 此外，虽然可以使用转义序列[从命令行设置选项卡标题](https://learn.microsoft.com/zh-cn/windows/terminal/tutorials/tab-title)，但目前无法以这种方式设置选项卡颜色。

## 鼠标交互

有几种方法可以使用鼠标与终端交互。

### 缩放

可以通过按住<kbd> ctrl </kbd>和滚动来缩放 Windows 终端的文本窗口（放大或缩小文本）。 缩放后，终端会话将保持新的缩放效果。 如果要更改字体大小，可参阅[配置文件 - 外观页](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/profile-appearance#text)，详细了解字体大小功能。

### 背景透明度

可按住<kbd> Ctrl </kbd> + <kbd> Shift </kbd>并滚动鼠标来调整背景的不透明度。 调整后，终端会话将保持新的不透明度。 如果要更改配置文件的 acrylic 不透明度，可参阅[配置文件 - 外观页](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/profile-appearance#transparency)，详细了解 acrylic 背景效果。

> 备注
> 
> 在 Windows 终端预览 1.12 版中，除非在设置中将 useAcrylic 设为 true，否则使用鼠标滚轮更改背景不透明度将默认使用复古样式的不透明度。 在 1.12 之前，终端始终使用 acrylic 以保持透明度。

### 超链接

可以按<kbd> ctrl </kbd>并用鼠标单击，从 Windows 终端内部打开超链接。

### 拖放文件/文件夹

可以将文件或文件夹拖放到“新建选项卡”按钮上，以在给定文件或文件夹中打开默认配置文件。 默认情况下，这将打开一个新选项卡。可以按住<kbd> Alt </kbd>在当前选项卡中打开一个新窗格，也可以按住<kbd> Shift </kbd>打开一个新窗口。

### 复制/粘贴

可以用鼠标右键单击，使用剪贴板存储在 Windows 终端中复制和粘贴文本。

Windows 终端还包括一个 [copyOnSelect](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/interaction#automatically-copy-selection-to-clipboard) 设置，可将其设置为 `true`，以便将用鼠标选择的任何文本立即复制到剪贴板。 在这种情况下，右键单击鼠标将始终执行粘贴操作。

### 虚拟终端何WSL鼠标支持

Windows 终端在适用于 Linux 的 Windows 子系统 (WSL) 应用程序以及使用虚拟终端 (VT) 输入的 Windows 应用程序中支持鼠标输入。 这意味着 [tmux](https://github.com/tmux/tmux/wiki) 和 [Midnight Commander](https://www.linuxhelp.com/how-to-install-midnight-commander-in-linux) 等应用程序将在你在终端窗口中选择项目时进行识别。 如果应用程序处于鼠标模式，你可以按住<kbd> shift </kbd>进行选择而不是发送 VT 输入。

## 使用密钥绑定发送输入命令

通过 Windows 终端可以使用密钥绑定将输入发送到 shell。 这可以通过 settings.json 文件的 `actions` 数组中的以下结构来完成。

``` json
{ "command": {"action": "sendInput", "input": ""}, "keys": "" }
```

如果需要，还可以添加 `"name": ""` 值。

### 清理屏幕

使用键盘快捷键向 shell 发送输入对于经常运行的命令非常有用。 清理屏幕就是一个示例：

``` json
{ "command": {"action": "sendInput", "input": "clear\r"}, "keys": "alt+k", "name": "clear terminal" }
```

### 导航到父目录

使用密钥绑定导航到父目录可能也很有用。

``` json
{ "command": {"action": "sendInput", "input": "cd ..\r"}, "keys": "ctrl+up" }
```

还可使用此功能运行生成或测试脚本。

## 焦点模式

“焦点模式”隐藏了通常位于 Windows 终端顶部的标题栏和选项卡，以便将你的注意力集中在终端内容上。 它类似于 Visual Studio Code 中的[“Zen 模式”](https://code.visualstudio.com/docs/getstarted/tips-and-tricks#_zen-mode)。

若要进入焦点模式，请使用<kbd> Ctrl </kbd> + <kbd> Shift </kbd> + <kbd> p </kbd> 打开[命令面板](https://learn.microsoft.com/zh-cn/windows/terminal/command-palette)，输入“焦点模式”，然后选择“切换焦点模式”。若要退出对焦模式，请重复上述步骤。

若要将焦点模式设置为每次启动 Windows 终端时启动，请打开“设置”(<kbd> Ctrl </kbd> + <kbd> , </kbd>) 并选择“启动”选项卡。在“启动模式”下，选择“焦点”（或“最大化焦点”，即终端窗口最大化的焦点模式）。 退出前选择“保存”。 下次启动 Windows 终端时，它将在焦点模式下打开。 若要阻止 Windows 终端以焦点模式启动，请执行相同的步骤，但从“启动模式”选项列表中选择“默认”。

若要添加用于进入焦点模式的快捷键（或键绑定），请打开 `settings.json` 文件 (<kbd> Ctrl </kbd> + <kbd> Shift </kbd> + <kbd> , </kbd> )。 在 `settings.json` 文件中，找到 `actions`: 部分并添加以下命令：

``` json
{ "command": "toggleFocusMode", "keys": "ctrl+f12" }
```

将<kbd> ctrl </kbd> + <kbd> f12 </kbd>替换为所选的快捷键/键绑定，但请确保不要重复操作列表中的任何现有键绑定。 还可以在 Windows 终端“设置”仪表板的“操作”选项卡中查看具有关联键绑定和“+ 新增”绑定的操作列表。 请记住在进行任何更改后选择“保存”。 现在可以使用创建的“操作”快捷键切换焦点模式。 （在我们的示例中，即<kbd> Shift </kbd> + <kbd> F12 </kbd>）。

若要了解有关此命令的详细信息，请参阅 [toggleFocusMode](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/actions#toggle-focus-mode)。

## Quake 模式

“Quake 模式”是终端在命名窗口 `_quake` 时进入的特殊模式的名称。 当窗口处于 quake 模式时：

- 终端会自动贴靠到监视器的上半部分。
- 无法再水平或从顶部调整窗口的大小。 只能在底部重设大小。
- 窗口会自动进入焦点模式（请注意，焦点模式下可能有多个选项卡）。
- 当 [windowingBehavior](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/startup#new-instance-behavior) 设置为 "useExisting" 或 "useAnyExisting" 时，它们将忽略 `_quake` 窗口的存在。
- 当最小化时，窗口将从任务栏中隐藏，并且按<kbd> Alt </kbd> + <kbd> Tab </kbd>时也不会显示。
- 一次只能有一个窗口处于 quake 模式。
 
可以通过绑定 `quakeMode` 操作或手动运行命令行来创建 quake 模式窗口：

``` powershell
wt -w _quake
```

> 备注
>
> 如果没有绑定 quakeMode 操作并将 quake 窗口最小化，将需要进入任务管理器才能退出该终端窗口！

## 更多资源

- [Windows 终端外观配置文件设置](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/profile-appearance?source=recommendations)

  了解如何自定义 Windows 终端内的外观配置文件设置。

- [Windows 终端操作](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/actions?source=recommendations)

  了解如何为 Windows 终端创建自定义操作。

- [Windows 终端配色方案](https://learn.microsoft.com/zh-cn/windows/terminal/customize-settings/color-schemes?source=recommendations)

  了解如何为 Windows 终端创建配色方案。

- [Windows 终端动态配置文件](https://learn.microsoft.com/zh-cn/windows/terminal/dynamic-profiles?source=recommendations)

  了解 Windows 终端中的动态配置文件。
