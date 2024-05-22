---
title: 学习streamlit-8
date: 2023-03-04 13:22:54
categories:
tags:
  - python
  - streamlit
---

## [Streamlit Components](https://docs.streamlit.io/library/components)

今天学习streamlit中定制组件（插件）的使用。

streamlit支持组件扩展，允许开发者开发自己的组件来扩展功能。组件是开发者编写的第三方python模块。

目前已经有大量第三方组件可供使用，比如streamlit官方收录的[典型组件](https://streamlit.io/components)和streamlit开发者Fanilo维护的[组件列表](https://discuss.streamlit.io/t/streamlit-components-community-tracker/4634)。

那我们该如何使用这些组件呢？非常简单，只需要`pip install`。

<!-- more -->

接下来我们演示一个非常常用的组件，[`streamlit_pandas_profiling`](https://share.streamlit.io/okld/streamlit-gallery/main?p=pandas-profiling)的使用。

首先，我们需要安装这个组件：

``` python
pip install streamlit_pandas_profiling
```

代码：

``` python
import streamlit as st
import pandas as pd
import pandas_profiling
from streamlit_pandas_profiling import st_profile_report

st.header('`streamlit_pandas_profiling`')

df = pd.read_csv('https://raw.githubusercontent.com/dataprofessor/data/master/penguins_cleaned.csv')

pr = df.profile_report()
st_profile_report(pr)
```

这段代码使用 `pandas-profiling` 库来生成一个数据报告，并将其在 Streamlit 应用程序中展示。

首先，导入了必要的库：Streamlit、Pandas 和 pandas-profiling。接下来，使用 `st.header()` 函数创建了一个标题，标题文本为 "streamlit_pandas_profiling"。

然后，使用 `pd.read_csv()` 函数从远程 CSV 文件加载数据集，并将其存储在名为 `df` 的 Pandas DataFrame 中。这个数据集是关于企鹅种类的，包含了每个企鹅的生理特征和物种分类。

接下来，使用 `profile_report()` 函数来生成一个数据报告。该函数会对 DataFrame 进行描述性统计分析、缺失值和重复数据的检测，并生成一份详细的 HTML 报告。生成的报告包含有关数据的各种信息，例如每个变量的统计数据、相关性、分布等。

最后，使用 `st_profile_report()` 函数将生成的报告展示在 Streamlit 应用程序中。这个函数可以将 pandas-profiling 生成的报告直接展示在 Streamlit 应用程序中，而不需要额外的 HTML 代码。

可以点击下方按钮查看程序运行效果：

[![Open in Streamlit](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://share.streamlit.io/dataprofessor/streamlit-components/)

这个应用程序是一个简单的演示，它展示了如何在 Streamlit 中使用 pandas-profiling 库来生成数据报告，并将报告展示在 Web 应用程序中。它可以帮助数据分析人员更轻松地了解他们的数据集，发现数据集中的问题，并快速生成有关数据集的详细信息。

可以通过下面两个视频来学习如何创建自定义组件来扩展streamlit功能。

<iframe src="//player.bilibili.com/player.html?bvid=BV1zv4y1h7Z9&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

<iframe src="//player.bilibili.com/player.html?bvid=BV1bT411Y7Qr&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
