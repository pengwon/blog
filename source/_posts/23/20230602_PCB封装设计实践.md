---
title: PCB封装设计实践
date: 2023-06-02 10:57:50
categories:
tags:
    - 硬件
    - ecad/eda
    - ad/altium designer
    - 技巧
---

在之前的文章 [详解AD(Altium Designer 23)中的机械层 - 1](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651480305&idx=1&sn=8830a49b4fbb34bbdf827ff9a32b621d&chksm=84ad7632b3daff24123c7dcfc296cc154bc506cf7b9306ac2daf228ddce474fa290e8e9a04ef#rd)、[详解AD(Altium Designer 23)中的机械层 - 2](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651480333&idx=1&sn=44e8269ed915b59e6fb72447c6a4cb6e&chksm=84ad77ceb3dafed890b5fadbacdf53b0bad075d05eb851b87feda84f78d4609d93241a8c3504#rd) 中，我们详细介绍了AD中机械层的相关设置，今天结合PCB封装的设计，给出一些实践建议，并分享一个文件模板。

<!-- more -->

## PCB封装设计

PCB（Printed Circuit Board）封装设计是指在设计电路板时，确定电子元件的尺寸、引脚排列和布局等关键参数，以便正确安装和连接元件。以下是进行PCB封装设计的一般步骤：

1. 收集元件信息：收集每个电子元件的数据手册或规格表，了解其尺寸、引脚定义、引脚间距、引脚排列方式等信息。

2. 创建封装库：使用PCB设计软件（如Altium Designer、Eagle、KiCad等）创建一个封装库，用于存储电子元件的封装。

3. 绘制封装图纸：使用PCB设计软件中的封装编辑器，根据元件的尺寸和引脚定义，绘制封装图纸。这包括绘制引脚、定义焊盘（Pad）的大小和形状、确定元件的外形轮廓等。

4. 校对封装图纸：仔细检查绘制的封装图纸，确保引脚位置正确、焊盘与元件引脚对应、外形符合尺寸要求等。

5. 导入封装库：将创建的封装图纸保存为封装文件，并导入到PCB设计软件的封装库中，以便在电路板设计中使用。

6. 应用封装：在PCB设计中，将相应的封装应用到每个元件上。这通常涉及选择正确的封装并将其放置在电路板布局中的适当位置。

7. 验证封装：在完成电路板布局后，进行封装验证。检查元件的封装与布局是否匹配，引脚和焊盘是否正确对齐，避免短路或连接错误。

8. 优化封装：如果验证过程中发现问题，可能需要进行封装优化。可能需要调整引脚位置、焊盘大小或形状等，以确保电子元件正确安装和连接。

9. 文档化封装：在完成封装设计后，及时更新封装库中的封装文件，并记录相关的尺寸、引脚定义和布局等信息，以备将来使用。

通常我们学习一个PCB设计软件时，最先接触的就是这个软件的原理图符号库和PCB封装库，然后学习如何创建原理图符号和PCB封装，所以基本上会用PCB设计软件的人最先会的就是画PCB封装。而原理图符号和PCB封装的设计，其实跟原理图设计和PCB设计一样，除了需要符合一些基本要求，还需要不断精进优化，对于现在的设计趋势而言，信息化、数字化正在加速发展，逐渐成为新的基本要求。具体在PCB工程中，就是各种信息的表达和关联，实践中，仍然有大量工程师先进行无参数绘图，然后在设计输出阶段再手动整理各种元器件信息、生产工艺、装配说明等等，事实上，我们可以优化设计流程，并充分利用软件功能，提高设计质量和效率。

## PCB封装信息表达

多数情况下，有一定工作经验的硬件工程师或PCB工程师，在PCB封装的电气参数设计上不会有很大差异，而且现在很多设计软件都内容了封装设计向导，可以直接产生符合标准（比如IPC-7351）的封装，今天主要来讲一讲电气以外的参数信息。

### 元器件层对

建立元器件PCB封装时，设置元器件层对可以为设计出图带来很大的便利。层对，顾名思义，就是这一层会跟随元器件放置在TOP或BOTTOM而相应变化，AD中顶铜层和底铜层、丝印层、阻焊层、钢网层是默认的层对。

将机械层添加为元器件层对时，它们将显示在“视图配置”面板的“元器件层对”部分中，如下所示。

![添加元器件层对](https://imgs.boringhex.top/blog/20230207113045.png)

- 可以定义任意数量的元器件层对。
- 图层对也可以分配图层类型。
- 在设计空间中，该对中的两个图层使用命名“Top <LayerPairName>”和“Bottom <LayerPairName>”显示在单独的图层选项卡上。
  
  ![图层名称自动关联](https://imgs.boringhex.top/blog/20230207115952.png)

- 在PCB库编辑器中，元件封装所需的其他设计对象放置在顶部对应层。当元件翻转到电路板的底部时，层对中顶层的内容会自动镜像到层对中的底层。
- 如果在PCB库中定义的元件层对分配了层类型，则在放置使用这些层的元件时，将在PCB上自动创建该层对。如果PCB已经具有该层类型的元件层对，则相应地映射这些层的内容。
- 对于PCB库中定义的元件层对，如果未在库中分配层类型，则会在PCB上创建单独的机械层。在这种情况下，在放置元件之前，使用相同的层号预定义PCB中的元件层对，因为如果无法按层类型匹配，软件将回退到按层号匹配。

我们在创建层对时，应指定图层类型，这样做有以下好处：

1. 管理机械层使用情况的常用方法是为每个所需的机械层功能分配专用层号。这种方法要求所有设计人员遵循相同的层分配和编号方案。当组件是从不遵循相同分配和编号方案的其他来源获得时，也会造成困难。如果使用了不同的方案，则必须将设计对象从其当前机械层移动到为该功能指定的机械层。
2. 此问题可以通过引入图层类型属性得到解决。当元件从一个元器件库放置到PCB编辑器中，或者从一个元器件库复制到另一个元器件库，或者由IPC封装向导创建时，无论分配给这些层类型的机械层数如何，都会自动匹配现有的层类型分配。对象将根据其图层类型重新定位到正确的图层上。如果软件无法按层类型匹配，它将回退到按层号匹配。
3. 对于单个机械层和元器件层对，您可以从预定义的类型列表中选择层类型。下图显示了可用图层类型的列表。您可以通过右键单击单个图层，然后从菜单中选择“编辑图层”或“添加组件层”命令来访问下面显示的对话框。

![指定图层类型](https://imgs.boringhex.top/blog/20230207114340.png)

实践中，建议在创建PCB封装时，至少设置以下层对：

- **3D Body** - 将此层用于元器件的[3D模型](https://www.altium.com/cn/documentation/altium-designer/working-with-3d-bodies)，了解有关[包含组件的3D模型](https://www.altium.com/cn/documentation/altium-designer/3d-advantage-ecad-mcad-integration#the-footprint-and-the-component)的详细信息。
- **Assembly** – 用于绘制/详图零部件的装配体数据。此层可以包含在[Draftsman](https://www.altium.com/cn/documentation/altium-designer/streamlining-board-design-documentation-with-draftsman)电路板装配视图中，然后可以在[Draftsman](https://www.altium.com/cn/documentation/altium-designer/streamlining-board-design-documentation-with-draftsman)元器件显示属性对话框中选择该层作为元器件的几何源。详细了解 [Draftsman](https://www.altium.com/cn/documentation/altium-designer/streamlining-board-design-documentation-with-draftsman)。
- **Component Center** – 用于指示元件的质心，在装配文档中提供元件贴装机使用的位置的视觉参考。
- **Component Outline** – 用于定义元件主体的轮廓，表示元件在电路板上占据的区域。
- **Courtyard** – 用于定义组件所需的放置空间。通常，Courtyard会勾勒出组件和焊盘的轮廓，并带有合适的间隙缓冲区。
  
  ![轮廓](https://imgs.boringhex.top/blog/20230207113845.png)
  
  绿色轮廓是Courtyard。详细了解[自定义封装创建](https://resources.altium.com/p/custom-footprint-creation-in-altium-designer)。在庭院图层上定义的形状还用于[组件选择](https://www.altium.com/cn/documentation/altium-designer/pcb-placement-editing-techniques#component_selection)、定义[组件区域](https://www.altium.com/cn/documentation/altium-designer/pcb-placement-editing-techniques#component_area)以及在组件中不包含3D模型时的[碰撞检测](https://www.altium.com/cn/documentation/altium-designer/pcb-design-rule-types#component_clearance)。
- **Designator** – 使用此图层放置特殊字符串。然后，可以将此层对包含在需要显示元件标号的装配体工程图中。详细了解.Designator[特殊字符串](https://www.altium.com/cn/documentation/altium-designer/pcb-placement-editing-techniques#string#special-strings)。

这样设置后，Draftsman可以根据PCB编辑器的装配元件层对上定义的形状，在电路板装配视图中定义每个元件轮廓的轮廓。Draftsman还支持在电路板装配视图中包括任何机械或元件层对，例如标号对。这些设置在选择`Board Assembly View`时在Draftsman的`Properties`面板中定义，极大提高出图质量和效率。

实践中，我一般将元器件中心画在元器件轮廓这一层，感觉这样更合理些。另外，可以把元器件引脚占用的PCB区域也画在这一层，在某些情况下会对layout起到提示作用。

![TSSOP20设计实例](https://imgs.boringhex.top/blog/20230530171356.png)

源文件下载链接👇

链接: https://pan.baidu.com/s/1dCOzJFKvhm6NFa91vjegLQ?pwd=1uqx 提取码: 1uqx 复制这段内容后打开百度网盘手机App，操作更方便哦 
