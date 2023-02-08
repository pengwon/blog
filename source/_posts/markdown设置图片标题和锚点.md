---
title: markdown设置图片标题和锚点
date: 2023-02-07 15:01:38
categories:
tags:
  - hexo
  - markdown
---

## 图片

markdown常用的图片语法：

``` markdown
![alt 属性文本](图片地址)
![alt 属性文本](图片地址 "可选标题")
```

<!-- more -->

实例：

``` markdown
<a id="display"></a> 

![](https://www.altium.com/documentation/sites/default/files/wiki_attachments/320796/MechLayersIn3d_1.png)

![示例图片](https://www.altium.com/documentation/sites/default/files/wiki_attachments/320796/LayerTypeCreation_Pcb_AD21.png)

![](https://www.altium.com/documentation/sites/default/files/wiki_attachments/320796/LayerTypeCreation_Pcb_AD21.png "示例图片")

![测试图片](https://www.altium.com/documentation/sites/default/files/wiki_attachments/320796/LayerTypeCreation_Pcb_AD21.png "示例图片")
```

实际显示效果：

<a id="display"></a> 

![](https://www.altium.com/documentation/sites/default/files/wiki_attachments/320796/MechLayersIn3d_1.png)

![示例图片](https://www.altium.com/documentation/sites/default/files/wiki_attachments/320796/LayerTypeCreation_Pcb_AD21.png)

![](https://www.altium.com/documentation/sites/default/files/wiki_attachments/320796/LayerTypeCreation_Pcb_AD21.png "示例图片")

![测试图片](https://www.altium.com/documentation/sites/default/files/wiki_attachments/320796/LayerTypeCreation_Pcb_AD21.png "示例图片")

注意实际效果与使用的markdown渲染引擎有关系，不同引擎可能会有差别，以上演示的是hexo博客的效果。

也可以像网址那样对图片网址使用变量：

``` markdown
[示例图片][1]

其它文字内容

[1]: https://www.altium.com/documentation/sites/default/files/wiki_attachments/320796/LayerTypeCreation_Pcb_AD21.png
```

实际显示效果：

[示例图片][1]

如果需要对图片应用一些属性，比如设置图片的高度和宽度，可以使用`<img>`标签。

``` markdown
<img src="https://www.altium.com/documentation/sites/default/files/wiki_attachments/320796/LayerTypeCreation_Pcb_AD21.png" width="50%">
```

实际显示效果：

<img src="https://www.altium.com/documentation/sites/default/files/wiki_attachments/320796/LayerTypeCreation_Pcb_AD21.png" width="50%">

## 锚点

有时需要在文章内设置锚点，以便可以快速跳转。

markdown中的标题可以自动跳转，使用URL语法，比如

``` markdown
点击[图片](#图片)，就会跳转到图片章节。
```

点击[图片](#图片)，就会跳转到图片章节。

如果需要跳转到非标题位置，可以在相应位置添加`<a></a>`标签，你可能已经注意到在文章开始处第一个图片位置添加了如下标签：

``` markdown
<a id="display"></a> 
```

在当前位置添加链接：

``` markdown
[跳转](#display)到第一个图片。
```

实际效果为：

[跳转](#display)到第一个图片。


[1]: https://www.altium.com/documentation/sites/default/files/wiki_attachments/320796/LayerTypeCreation_Pcb_AD21.png
