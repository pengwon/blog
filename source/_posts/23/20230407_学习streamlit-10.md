---
title: 学习streamlit-10
date: 2023-03-08 10:45:01
categories:
tags:
  - python
  - streamlit
---

[`st.file_uploader`](https://docs.streamlit.io/library/api-reference/widgets/st.file_uploader)

`st.file_uploader`是一个文件上传组件，默认配置下，上传的文件大小限制在200MB以内，可以通过配置`server.maxUploadSize`选项修改这一限制。了解更多[配置选项](https://docs.streamlit.io/library/advanced-features/configuration#set-configuration-options)。

### demo

点击按钮运行演示程序：

[![Open in Streamlit](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://share.streamlit.io/dataprofessor/st.file_uploader/)

<!-- more -->

运行效果：

<iframe src="//player.bilibili.com/player.html?bvid=BV1ZL41117Y4&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

在演示程序中，我准备了一个沪深300指数近一年的csv格式的数据，上传后将数据和概要显示出来。

代码：

``` python
import streamlit as st
import pandas as pd

st.title('st.file_uploader')

st.subheader('Input CSV')
uploaded_file = st.file_uploader("Choose a file")

if uploaded_file is not None:
  df = pd.read_csv(uploaded_file)
  st.subheader('DataFrame')
  st.write(df)
  st.subheader('Descriptive Statistics')
  st.write(df.describe())
else:
  st.info('☝️ Upload a CSV file')
```

这段代码创建一个网络应用程序，允许用户上传CSV文件并显示数据的一些描述性统计。

-   `import streamlit as st`：导入Streamlit库。
    
-   `import pandas as pd`：导入pandas库，用于数据操作和分析。
    
-   `st.title('st.file_uploader')`：将网页应用程序的标题设置为“st.file\_uploader”。
    
-   `st.subheader('Input CSV')`：在网页上显示子标题“Input CSV”。
    
-   `uploaded_file = st.file_uploader("Choose a file")`：创建一个文件上传器小部件，允许用户选择并上传文件。上传的文件存储在变量`uploaded_file`中。
    
-   `if uploaded_file is not None:`：检查是否上传了文件。如果`uploaded_file`不是`None`，则在`if`语句下方的代码块中执行以下操作：
    
    -   `df = pd.read_csv(uploaded_file)`：将上传的CSV文件读入到pandas DataFrame中，存储在变量`df`中。
    -   `st.subheader('DataFrame')`：在网页上显示子标题“DataFrame”。
    -   `st.write(df)`：在网页上显示DataFrame中的数据。
    -   `st.subheader('Descriptive Statistics')`：在网页上显示子标题“Descriptive Statistics”。
    -   `st.write(df.describe())`：在网页上显示DataFrame的一些描述性统计信息。
-   `else:`：如果没有上传文件，则在网页上显示“☝️ Upload a CSV file”的信息。
