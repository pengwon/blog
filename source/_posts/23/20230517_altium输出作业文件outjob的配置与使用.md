---
title: altium输出作业文件outjob的配置与使用
date: 2023-05-17 11:54:09
categories:
tags:
    - ad/altium designer
    - ecad/eda
---

## 1. OutJob 简介

AD中，对于一些常见的打印输出，例如 Gerber、丝印、原理图、BOM、STEP 等制作文件，配置几乎是相同的。如果每次都要进行类似于 [File] → [Smart PDF] 这样的重复操作，将是非常繁琐的。在这种情况下，可以创建一个预配置的输出文件，以简化设计文件的输出流程。这就是 OutJob。

OutJob 可以针对每种特定类型的输出配置自己的设置和输出格式。因此，可以使用单个 .OutJob 来为从项目生成的所有此类输出配置属性，包括 gerber、丝印、原理图、BOM、STEP 等等。此外，OutJob 具有高度灵活性，因为可以根据需要为不同的输出容器配置输出文件，这使得可以关联任意数量的输出容器与单个文件。这种灵活性可大大简化设计文件的输出流程，并有助于保证输出类型的规范化。

除了这些优势之外，OutJobs 还提供验证类型的检查功能，如ERC 和 DRC 报告。这些报告可以在生成输出之前用于最终的检查，并且可作为记录发布设计准备已完成的证明文件保存。

<!-- more -->

以下是之前为 2 层板工程创建的预配置 OutJob 文件，其中包含了与生产加工及采购相关的各种输出容器。此外，所有的相关输出文件都已配置完成，并与其对应的容器相关联。这样一来，在导出输出文件时就可以轻松地从该文件中获得所需的结果了。

![2层板工程OutJob模板展示](https://imgs.boringhex.top/blog/成品模板展示.jpg)

此模板调用时只需要将该.OutJob文件添加进相应的工程中（右击工程文件→Add existing to project ），然后打开.OutJob文件,修改[PCB Document]为工程文件名称后，点击右侧相应输出容器中的生成内容(Generate content)即可完成设计文件的输出。

将.OutJob文件添加到项目后，会显示在“Project”面板中的“settings\Output Job Files”子文件夹下。

下面简要介绍.OutJob的配置过程。

## 2. 创建一个.OutJob

### 2.1 添加和定义OutJob

创建新的输出作业文件：**[File » New » Output Job File]**

![创建OutJob](https://imgs.boringhex.top/blog/创建OutJob.jpg)

> OutJob文件的构成要素如上图所示, 这些要素均在OutputJob Editor中定义和管理。

OutJob包含以下几个部分：

**1.设置变体选择**——Altium Designer允许使用基本（非可变）设计或指定使用该设计的定义变体来驱动PCB项目的输出。分别为每个适用的输出选择一个变量，或者选择一个单独的变量应用于文件中的所有适用输出。

**2.添加和配置所需输出**——输出被收集到功能类别中，如装配输出(Assembly)、制造输出(Fabrication)和报告输出(Report)。严格来说，输出是通过运行相关的输出生成器来获得的，该生成器经过适当配置，并使用项目（或项目本身）中的指定文档作为其数据源。

**3.添加和配置所需的输出格式**——生成任何给定的输出类型都需要将输出映射到相应的（和适用的）输出格式。这采用支持的输出容器（PDF、文件夹结构、视频）或硬拷贝（基于打印的输出）之一的形式。多个输出可以映射到同一个容器或硬拷贝，用户可以控制输出的生成位置和方式（与容器/硬拷贝相关的任何媒体相关选项）。

### 2.2 在OutJob内配置输出

单击类别底部相应的Add new[type]output文本，然后从弹出菜单中选择所需的输出类型，添加所需类型的新输出(或者也可以从“Edit”主菜单中选择相关的命令条目)。通过选择适当的数据源来添加所需的每个输出。

本文以配置原理图输出文件为例，首先在类别“**Documentation Outputs**”中点击[**Add New Documentation Output » Schematic Prints » Project Physical Documents**]（这里数据源选择的是项目实物文档，如果想单独打印某个SCH选择其作为数据源即可），操作如下图所示：

![配置原理图输出](https://imgs.boringhex.top/blog/配置原理图输出.jpg)

选择完数据源后，根据个人需要可以点击进去做一些个性化的输出参数配置，如下图所示配置原理图输出为A4纸张大小，颜色为Gray等内容：

![原理图输出属性配置](https://imgs.boringhex.top/blog/原理图输出属性配置.jpg)

根据具体的输出类型，可以使用选项来配置相关的输出生成器，从而对生成的输出提供更多的控制。在配置选项可用的情况下，可以通过以下方式之一访问这些选项：

1. 直接在行中双击以获得所需的输出。
2. 右键单击所需的输出，然后从上下文菜单中选择“Configure”。
3. 选择所需的输出，然后使用键盘快捷键Alt+Enter。
4. 选择所需的输出，然后选择 Edit » Configure命令。

不同的输出生成器有独特的对话框来精确配置生成的内容。启动命令后，将显示相关的配置对话框。使用该对话框可以定义要生成的特定输出文件的设置选项。定义的任何选项都将在下次生成该输出时使用。

## 3. 创建OutJob输出容器

为OutJob添加和配置输出文件需要了解生成的内容以及如何生成。需要定义生成的输出要写入的位置，即生成输出的格式。根据生成的输出类型，这将使用输出容器和硬拷贝的组合来处理。

### 3.1 输出容器说明

输出可以写入(适用的)三种类型的输出容器——PDF、特定格式的输出文件（如Gerber文件）或视频。

当创建一个新的OutJob时将默认包含这些类型的容器中的一个，分别命名为PDF, Folder Structure, and Video。可以通过单击[**Add New Output Container**]或**Edit » Add Output Medium**菜单添加新的容器，并且可以编辑名称以便于识别。
### 3.2 配置输出容器

还是以原理图输出为例，建立一个PDF容器，更改命名为SCH_PDF，一个容器可以关联添加多个输入，如下图所示除了原理图还加入了PCB及PCB 3D图纸的输出，当点击容器里边的生成内容后，这些输出会打印在同一份PDF文件中。

> 注意：输出容器选择是根据输出内容来决定的，如果要输出gerber、step这种有固定文件格式的则需要选择Folder Structure文件夹结构的容器，丝印图原理图可选择PDF容器。

![输出容器配置](https://imgs.boringhex.top/blog/SCH容器配置举例1.jpg)

关联添加输出文件后，还需要给容器配置输出路径，建议所有输出容器第一级文件夹名称一致，这样所有容器生成的输出文件均会在同一个文件夹下新建子目录文件，方便查找归类。本文举例的容器选择的是手动配置的名为Outputs文件夹，如下图所示点击change会弹出路径设置对话框：

![SCH容器配置](https://imgs.boringhex.top/blog/SCH容器配置2.jpg)

根据个人喜好配置合适的输出路径：

![SCH容器路径配置](https://imgs.boringhex.top/blog/SCH容器路径配置.jpg) 

## 4. OutJob的调用

OutJob文件内输出的配置基本相同，gerber的输出会因为PCB板层的不同有些差异，所以建议针对不同层数的PCB工程文件单独配置一个.OutJob文件。

配置好的OutJob调用时只需要将其添加进目标项目工程中(右击工程文件→Add existing to project )。OutJob文件添加到项目后，会显示在“Project”面板中的“settings\Output Job Files”子文件夹下。然后打开.OutJob文件,修改[PCB Document]数据源为项目工程文件后，点击右侧相应输出容器中的生成内容(Generate content)即可完成设计文件的输出，详细步骤如下图所示：

![OutJob文件的调用](https://imgs.boringhex.top/blog/OutJob文件的调用.jpg)

如下图所示，项目工程所在文件夹下会自动生成之前在.OutJob文件容器中设置的名为Outputs的文件夹，生成的输出文件则存放在该文件内：

![生成文件](https://imgs.boringhex.top/blog/生成文件.jpg)   
