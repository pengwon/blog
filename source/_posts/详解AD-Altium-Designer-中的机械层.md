---
title: 详解AD(Altium Designer)中的机械层
date: 2023-02-06 15:59:37
categories:
tags:
  - ecad/eda
  - altium
  - 硬件
---

再简单的电路板也需要一些线和焊盘以外的设计细节，比如板子的尺寸、加工说明，或者元器件的占用区、3d模型等。在ad(altium)软件中，可以把这些细节信息附加到机械层上。

在ad中，有两种类型的机械层可供使用：

1. 单独机械层，通常被用作那些与电路板哪一面无关的信息，比如板边。
2. 成对的机械层，通常用作元器件相关的附加信息，比如元器件的占用区，显然与元器件放在板子的哪一面有关。因为成对的层与元器件相关，所以被叫作"元器件层对"。

不管哪种类型，都可以根据需要在打印输出或者生产输出中被包含。

<!-- more -->

## 添加机械层到设计

机械层的添加、编辑和删除，在[View Configuration](https://www.altium.com/cn/documentation/altium-designer/pcb-colors-visibility)面板，他们的显示状态也在这个面板设置。点击工作区右下角的`Panels`，

![](https://imgs.boringhex.top/blog/20230206174109.png)

然后选择`View Configuration`，或者使用快捷键`L`，可以显示`View Configuration`面板。

在`Layers`区任意地方点击右键就会显示上下文菜单，其中提供了`Add Component Layer Pair`和`Add Mechanical Layer`命令。

> 软件中没有限制可添加机械层的数量，但是由实际情况，能少则少。

![](https://imgs.boringhex.top/blog/20230207114004.png)

元器件层对或机械层的设置在`Edit Layers Pair / Edit Layer`对话窗口进行，可以输入层名称和编号，并分配[层类型](#层类型)。

<a id="edit-layer"></a>

![](https://imgs.boringhex.top/blog/20230207103251.png)

![](https://imgs.boringhex.top/blog/20230207103344.png)

- 当层对或层被添加时，如果没编辑层名称，软件会根据选择的层类型自动命名。
- 如果层对或层被重命名，层类型会在`View Configuration`面板中层名称旁边的括号中显示。
- 当设置层编号时，如果想设置的编号已经被占用了会有警告。

层对和层可以按照名称或编号进行排序，在`Preferences`窗口的[PCB Editor – General page](https://www.altium.com/cn/documentation/altium-designer/accessing-defining-managing-system-preferences#general_pcb)进行设置。

虽然旧版本的软件最多只支持32个机械层，但是新版本设计的PCB文件如果包含更多的机械层依然可以用旧版打开和编辑。

编辑存在的层的操作也是在`View Configuration`面板中通过双击或右键完成，比较简单。

要删除层的话，与其他编辑操作差不多，但根据层上元素有以下几种情况：

- 如果层上包含元器件元素，此时不能删除，会弹出警告。
- 如果层上包含的元素可被删除，会弹出确认删除提示信息。
- 如果层上没有元素，则没有任何提示直接删除。

## 层类型

正如前面提到的，ad中有2种类型的机械层，1种是元器件层对，1种是单独机械层，层类型在[Edit Layer](#edit-layer)对话窗口的 `Layer Type`下拉列表中选择。

### 元器件层对

- **3D Body** - 将此层用于元器件的[3D模型](https://www.altium.com/cn/documentation/altium-designer/working-with-3d-bodies)，了解有关[包含组件的3D模型](https://www.altium.com/cn/documentation/altium-designer/3d-advantage-ecad-mcad-integration#the-footprint-and-the-component)的详细信息。
- **Assembly** – 用于绘制/详图零部件的装配体数据。此层可以包含在[Draftsman](https://www.altium.com/cn/documentation/altium-designer/streamlining-board-design-documentation-with-draftsman)电路板装配视图中，然后可以在[Draftsman](https://www.altium.com/cn/documentation/altium-designer/streamlining-board-design-documentation-with-draftsman)元器件显示属性对话框中选择该层作为元器件的几何源。详细了解 [Draftsman](https://www.altium.com/cn/documentation/altium-designer/streamlining-board-design-documentation-with-draftsman)。
- **Coating** – 用于定义需要保护涂层的组件区域。
- **Component Center** – 用于指示元件的质心，在装配文档中提供元件贴装机使用的位置的视觉参考。
- **Component Outline** – 用于定义元件主体的轮廓，表示元件在电路板上占据的区域。
- **Courtyard** – 用于定义组件所需的放置空间。通常，庭院会勾勒出组件和焊盘的轮廓，并带有合适的间隙缓冲区
  
  ![](https://imgs.boringhex.top/blog/20230207113845.png)
  
  绿色轮廓是庭院。详细了解[自定义封装创建](https://resources.altium.com/p/custom-footprint-creation-in-altium-designer)。在庭院图层上定义的形状还用于[组件选择](https://www.altium.com/cn/documentation/altium-designer/pcb-placement-editing-techniques#component_selection)、定义[组件区域](https://www.altium.com/cn/documentation/altium-designer/pcb-placement-editing-techniques#component_area)以及在组件中不包含3D模型时的[碰撞检测](https://www.altium.com/cn/documentation/altium-designer/pcb-design-rule-types#component_clearance)。
- **Designator** – 使用此图层放置特殊字符串。然后，可以将此层对包含在需要显示元件标号的装配体工程图中。详细了解.Designator[特殊字符串](https://www.altium.com/cn/documentation/altium-designer/pcb-placement-editing-techniques#string#special-strings)。
- **Dimensions** – 用于定义组件所需的尺寸细节。
- **Glue Points** – 用于定义组件粘附点。
- **Gold Plating** – 用于定义组件选择性镀金要求。
- **Value** – 使用此层放置特殊字符串。然后，可以将此层对包含在需要显示元件值的装配体工程图中。详细了解.Comment[特殊字符串](https://www.altium.com/cn/documentation/altium-designer/pcb-placement-editing-techniques#string#special-strings)。

如上所述，Draftsman可以根据PCB编辑器的装配元件层对上定义的形状，在电路板装配视图中定义每个元件轮廓的轮廓。Draftsman还支持在电路板装配视图中包括任何机械或元件层对，例如标号对。这些设置在选择`Board Assembly View`时在Draftsman的`Properties`面板中定义。详细了解 [Draftsman](https://www.altium.com/cn/documentation/altium-designer/streamlining-board-design-documentation-with-draftsman)。


### 机械层

- Assembly Notes - 用于详细说明组件加载顺序和/或重要的装配说明。
- Board – 使用此层获取与电路板相关的说明或详细信息。
- Board Shape – 将此图层用于整个电路板轮廓（[电路板形状](https://www.altium.com/cn/documentation/altium-designer/pcb-obj-boardshapeboard-shape-ad)）。当电路板区域转换为自由基元时，将自动创建此机械层类型以保存基元（板规划模式，工具 » 转换 » 分解区域为自由基元）。作为使用“板形状”层类型定义机械层形状的替代方法，Gerber 标准支持在“Gerber 设置”或“Gerber X2 设置”对话框中启用 Gerber 电路板轮廓时自动生成电路板轮廓制造文件。此板轮廓直接来自实际的板形状，而不是放置在具有板形状的层类型的机械层上的对象。了解有关直接从电路板形状生成 [Gerber 电路板轮廓](https://www.altium.com/cn/documentation/altium-designer/generating-fabrication-data#gerber-board-profile)的更多信息。
- Dimensions – 用于定义电路板所需的尺寸细节。
- Fab Notes – 用于详细说明重要的制造说明。
- Route Tool Path – 用于指示包含机械布线信息的图层。请注意，使用此图层类型 （[显示图片](#RouteToolPath))。
- Sheet – 使用此图层可以定义外部文档绘图模板边框。有关详细信息，请参阅下面的[图纸表示和设置](#工作表展示和设置)部分。
- V Cut – 用于定义 V 形切割细节。V 形切割用于通过在电路板的顶部和底部切割“V”形凹槽来分割电路板，同时留下最少的材料将电路板面板固定在一起.

## 显示机械层

- 单击图层名称旁边的可见性图标以打开或关闭该机械层的显示，或选择图层名称并按空格键（这将切换组件层对中两个图层的显示）。
- 机械层具有额外的显示功能;它们可以设置为在显示处于单层模式时保持可见。按住 Ctrl 并单击机械层的可见性图标以启用以单层模式显示功能。可见性图标将发生变化，以指示此图层已启用此功能 （），再次按住 Ctrl+单击可禁用此模式。单层模式是一种方便的显示控制功能，其中所有其他层都隐藏在活动（当前）层之外。在设计空间中按 Shift+S 以循环切换启用的单层模式，然后返回到上一层显示状态。当显示器处于单层模式时，非活动图层可以完全隐藏、设置为灰色或设置为单色。可用的单层模式选项在“首选项”对话框的“[PCB 编辑器 – 电路板智能显示](https://www.altium.com/cn/documentation/altium-designer/accessing-defining-managing-system-preferences#board_insight_display)”页面中配置。
- 当 3D 设置使用颜色 - 按图层时，机械层也可以包含在 3D 显示中。将显示当前配置为可见的机械层。

![](https://imgs.boringhex.top/blog/msedge_1SlUpO3tub-min.gif)

## 机械层和组件层对

在某些情况下，机械层上包含的额外细节只需要一次，例如，详细说明组件加载顺序和重要装配说明的装配说明。在这种情况下，将添加、命名标准机械层，并在可能的情况下分配其层类型（详见下文）。

如果组件需要额外的细节，例如组件庭院轮廓，则需要分配两个机械层：当组件放置在电路板的顶部时，一层包含庭院细节;如果将组件翻转到电路板的底部，则另一个机械层保持相同的庭院细节。

在这种情况下，一对机械层将添加为组件层对。将机械层添加为组件层对时，它们将显示在“视图配置”面板的“组件层对”部分中，如下所示。

![A number of user-defined Component Layer Pairs have been added.](https://imgs.boringhex.top/blog/20230207113045.png)

- 可以定义任意数量的组件层对。
- 图层对也可以分配图层类型。
- 在设计空间中，该对中的两个图层使用命名“顶部<LayerPairName>”和“底部<LayerPairName>”显示在单独的图层选项卡上。
  
  ![](https://imgs.boringhex.top/blog/20230207115952.png)

- 在PCB库编辑器中，元件封装所需的其他设计对象放置在顶部对层。当元件翻转到电路板的底部时，成对中顶层的内容会自动镜像到对中的底层。
- 如果在PCB库中定义的元件层对分配了层类型，则在放置使用这些层的元件时，将在PCB上自动创建该层对。如果PCB已经具有该层类型的元件层对，则相应地映射这些层的内容。
- 对于PCB库中定义的元件层对，如果未在库中分配层类型，则会在PCB上创建单独的机械层。在这种情况下，在放置元件之前，使用相同的层号预定义PCB中的元件层对，因为如果无法按层类型匹配，软件将回退到按层号匹配。

## 分配图层类型的优势

管理机械层使用情况的常用方法是为每个所需的机械层功能分配专用层号。这种方法要求所有设计人员遵循相同的层分配和编号方案。当组件是从不遵循相同分配和编号方案的其他来源获得时，也会造成困难。如果使用了不同的方案，则必须将设计对象从其当前机械层移动到为该功能指定的机械层。

此问题已通过引入图层类型属性得到解决。当元件从一个元器件库放置到PCB编辑器中，或者从一个元器件库复制到另一个元器件库，或者由IPC封装向导创建时，无论分配给这些层类型的机械层数如何，都会自动匹配现有的层类型分配。对象将根据其图层类型重新定位到正确的图层上。如果软件无法按层类型匹配，它将回退到按层号匹配。

对于单个机械层和组件层对，您可以从预定义的类型列表中选择层类型。下图显示了可用图层类型的列表。您可以通过右键单击单个图层，然后从菜单中选择“编辑图层”或“添加组件层”命令来访问下面显示的对话框。

![Select the Layer Type from the pre-defined list of Types; individual mechanical layers are shown on the left; Component Layer Pairs are shown in the middle and a new Component Layer on the right.](https://imgs.boringhex.top/blog/20230207114340.png)

## 使用分配的图层类型命名图层

分配图层类型后，图层会自动将其“图层名称”属性更改为与“图层类型”相同。如果需要，可以通过键入用户定义的名称来覆盖此名称。当图层具有用户定义的名称和分配的图层类型时，两者都会显示在括号中显示的图层类型，如下所示的图层对。

![](https://imgs.boringhex.top/blog/20230207114750.png)

请注意，从 1 到 32 的层数始终显示在单个机械层或组件层对的“视图配置”面板中。对于分配了层类型的单个机械层或组件层对，不会显示较高的层数。

## Route工具路径图层类型

分配图层类型时刚刚描述的命名行为有一个例外 - 当图层类型设置为 .原因是旧版本的软件使用“路径工具路径”图层的名称来标识包含路径信息（也称为路径信息）的图层。修复此层的命名可确保设计在旧版本中继续正常运行。

“Route工具路径”图层类型用于指示包含机械布线信息的图层。使用此层的典型方法是在电路板形状的外边缘周围放置轨道和圆弧，以定义加工路径和宽度。留下实心部分以将电路板固定在面板内，然后在每个固体部分上放置一系列小孔以产生穿孔（通常称为鼠标咬合），允许在组装过程完成后将电路板从面板上卡出。

当电路板以 3D 模式显示时，在“Route工具路径”图层上检测到的对象将显示为电路板中的Route槽，如下所示。

<a id="RouteToolPath"></a>
![Objects detected on the Route Tool Path layer are used to visualize the routed board, in 3D display mode.](https://imgs.boringhex.top/blog/20230207115007.png)

在“Route工具路径”图层上检测到的对象用于在 3D 显示模式下可视化布线板。

使用“[板形状中的线/弧图元](https://www.altium.com/cn/documentation/altium-designer/defining-board-shape#creating_primitives_from_the_board_shape)”对话框可以使用轨迹和圆弧跟踪板形状的外部。启用对话框中的“Route Tool Outline”选项，将对象放置在板形状之外，而不是沿其边缘居中。一些设计人员在使用[嵌入式电路板阵列功能创建拼板](https://www.altium.com/cn/documentation/altium-designer/creating-board-fabrication-panel)时，更喜欢添加制造信息，而不是在实际的电路板文件中包含此详细信息。

了解有关[电路板拼板和定义Route工具路径](https://www.altium.com/cn/documentation/altium-designer/creating-board-fabrication-panel)的更多信息。

## 从编号机械层过渡到层类型

如果可能，建议编辑源库并指定图层类型。从库中放置（或复制）元件封装时，这些层类型的机械层和元件层对（如果不存在）将自动在目标板（或库）中创建。如果目标板（或库）中已存在这些图层类型，则图层内容将自动映射到正确的图层。

![User layers with a Layer Type assigned are automatically created or mapped when the component is placed onto a board. Hover the cursor over to show the component on the board.](https://imgs.boringhex.top/blog/msedge_oTkQDBi2ov.gif)

## 工作表展示和设置

添加类型的机械层以定义外部文档绘图模板边框。

![](https://imgs.boringhex.top/blog/20230207122821.png)

可以在“板”模式下的“[属性](https://www.altium.com/cn/documentation/altium-designer/pcb-environment-setup#properties_panel)”面板中根据图纸左下角的 X/Y 值、宽度和高度自定义图纸设置。如果启用了“从图纸图层获取大小”选项，则根据放置在机械图层上的一组对象的边界矩形自动计算图纸背景。

![](https://imgs.boringhex.top/blog/20230207122841.png)

工作表背景的颜色和可见性在“[视图配置](https://www.altium.com/cn/documentation/altium-designer/pcb-colors-visibility)”面板的“系统颜色”部分中配置。单击颜色按钮以更改图纸线和区域颜色的颜色。切换到/显示/隐藏工作表。

![](https://imgs.boringhex.top/blog/20230207122906.png)

## 在输出中包含机械层

机械层用于各种任务，详细说明电路板设计、制造、组装和产品文档使用的信息。为了支持所有这些要求，机械层可以排除或包含在所有形式的基于层的输出生成中，包括打印和输出文件生成。

### 打印输出

设计中存在的任何层都可以包含在PCB打印输出的规范中，包括机械层。通过添加所需的图层并在“打印”对话框中设置其顺序来配置打印输出。

![Highly detailed fabrication and assembly drawings can be created by placing objects on mechanical layers.](https://imgs.boringhex.top/blog/20230207140339.png)

了解有关[准备基于 2D 打印的 PCB 输出](https://www.altium.com/cn/documentation/altium-designer/configuring-pcb-printouts#preparing_2d_print_based_pcb_output)的更多信息。

该软件还包括一个先进而灵活的图形编辑环境，用于创建电路板设计生产文档，称为Draftsman。Draftsman 绘图系统配有一组专用的绘图工具，提供了一种交互式方法，将制造和装配图与自定义模板、注释、尺寸、标注和注释结合在一起。

了解更多关于[Draftman](https://www.altium.com/cn/documentation/altium-designer/streamlining-board-design-documentation-with-draftsman)的信息。

### 生成的输出

所有制造类型的输出（如 Gerber 和 ODB++）都允许将机械层作为输出层包含在“绘图”中，或作为细节添加到正在绘制的每个层中。当使用输出作业配置文件（*.OutJob）中的 ODB 输出生成器运行配置的输出生成器时，将生成输出。

![Mechanical layers can be plotted, or they can be added to all plots if required.](https://imgs.boringhex.top/blog/20230207140950.png)

了解有关[准备制造设计](https://www.altium.com/cn/documentation/altium-designer/preparing-your-design-for-manufacture-overview)的更多信息。
