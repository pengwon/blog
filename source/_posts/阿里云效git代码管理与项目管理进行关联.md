---
title: 阿里云效git代码管理与项目管理进行关联
date: 2023-05-05 17:22:02
categories:
tags:
  - git
  - ci/cd
---

阿里云的文档写的还是非常不错，但是有很多内容太过于分散，今天就codeup代码仓库与项目协作projex之间的关联做一些实践总结。

需要了解阿里云效平台的可以看这篇文章👉 [阿里云效devops平台简介](https://blog.boringhex.top/posts/77c97b5d5e37/)

<!-- more -->

首先打开阿里云效项目协作[Projex项目页面](https://devops.aliyun.com/projex/project) ：

![Projex项目页面](https://imgs.boringhex.top/blog/20230504153657.png)

点击右侧“创建新项目”，会弹出一个新建项目向导：

![新建项目向导](https://imgs.boringhex.top/blog/20230504153907.png)

这里根据项目特征选择一个模板，比如是一个新开发项目，则可以选择“敏捷”或“经典”；如果是一个维护项目，则可以选择“缺陷”或“反馈”。上图所示的这些模板都是内置的，也可以根据自身团队实践来自定义一些模板，点右上角的“模板管理”则可以对模板进行管理和自定义。项目模板只是将云效平台提供的功能做了重新组合以更好适应项目管理，更便于操作。新建项目后依然可以根据实际需要对功能进行重新组合，所以这里也不必太纠结，大致选一个开始就好。

下面以内置的“敏捷研发示例项目”为例，点进去：

![敏捷研发示例项目](https://imgs.boringhex.top/blog/20230504154952.png)

这个就是项目首页，在这个页面可以看到项目概况，以在右上角“编辑概览”中对页面布局和可视项进行设置。我们重点关注“统计”中的内容，现在进行中的需求有10个，活动缺陷有4个，未完成任务是10个。下面我们来用实例说明如何将代码管理与项目协作进行关联。

点击“进行中的需求”，

![进行中的需求](https://imgs.boringhex.top/blog/20230504160121.png)

可以设置筛选条件对需求进行分类展示，上上面点进来默认是进行中的需求，点一个，比如“将研发过程产生的研发资产关联到需求”：

![将研发过程产生的研发资产关联到需求](https://imgs.boringhex.top/blog/20230504160917.png)

将代码管理Codeup中的示例仓库clone到本地：

``` powershell
# 注意这个示例仓库地址是小编的，需要将仓库地址替换成自己的。
❯ git clone https://codeup.aliyun.com/linq-tech/Codeup-Demo.git
❯ cd .\Codeup-Demo\ && ls

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d----            2023/5/4    16:15                src
-a---            2023/5/4    16:15            364 .gitignore
-a---            2023/5/4    16:15          10389 mvnw
-a---            2023/5/4    16:15           6792 mvnw.cmd
-a---            2023/5/4    16:15           2039 pom.xml
-a---            2023/5/4    16:15            626 README.md
```

## 关联分支到工作项

projex中的需求、任务、缺陷都是工作项的类型，在项目中统一分配工作项id，比如上面的“将研发过程产生的研发资产关联到需求”的ID为"DEMO-18"(点击工作项后在左上角显示，可以一键复制)。我们可以使用特定关键字将分支与工作项关联，以`/ID`结尾的分支会关联到相应ID的工作项。比如`feature/DEMO-18`分支会自动关联到“将研发过程产生的研发资产关联到需求”。

``` powershell
# 新建并切换到 feature/DEMO-18 分支
❯ git checkout -b feature/DEMO-18
Switched to a new branch 'feature/DEMO-18'

# 然后 push 到远端仓库
❯ git push --set-upstream origin "feature/DEMO-18"
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
To https://codeup.aliyun.com/linq-tech/Codeup-Demo.git
 * [new branch]      feature/DEMO-18 -> feature/DEMO-18
branch 'feature/DEMO-18' set up to track 'origin/feature/DEMO-18'.
```

然后再到projex对应工作项页面，可以看到这个分支已经关联上了：

![关联codeup代码仓库分支到projex](https://imgs.boringhex.top/blog/20230504174013.png)

## 关联提交到工作项

与关联分支类似，关联提交到工作项是以`#ID`作为关键字，只要在commit message中包含`#ID`则会关联这个提交到相应ID工作项。

``` powershell
# 在标题行开始
  #DEMO-18

# 包含在标题行
test #DEMO-18

# 包含在消息体
test

  #DEMO-18
  modified:   README.md
```

这几种方式都可以，但是需要注意的是，以`#`开始的行会被git认为是注释，所以要以`#ID`开始的话，需要在`#ID`前面加个空格。

![关联提交到工作项](https://imgs.boringhex.top/blog/20230504185323.png)

## 关联合并请求到工作项

合并请求是通过标题中`#ID`关键字与工作项关联，但是我多次尝试都没有成功。不知道是不是因为合并请求这个操作需要在网页端操作，所以就把这个关键字关联的功能给砍掉了，而只能在合并请求页面操作关联工作项。
