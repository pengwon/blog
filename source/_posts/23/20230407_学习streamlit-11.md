---
title: 学习streamlit-11
date: 2023-03-10 11:04:18
categories:
tags:
  - python
  - streamlit
---

## [`st.plotly_chart`](https://docs.streamlit.io/library/api-reference/charts/st.plotly_chart)

今天学习`st.plotly_chart`的使用，并完成一个股票蜡烛图的练习。

`st.plotly_chart`用来显示交互式绘图表，Plotly是一个Python的图表库。

`st.plotly_chart`函数的参数跟 Plotly 的 plot（） 函数几乎一样，你可以在 https://plot.ly/python 找到更多关于Plotly的信息。

要在 Streamlit 中显示 Plotly 图表，只需要在通常调用 Plotly 的 py.plot 或 py.iplot 函数的地方替换成调用 st.plotly_chart 。

<!-- more -->

先来看一段在独立使用plotly的python代码：

``` python
import plotly.figure_factory as ff
import numpy as np

x1 = np.random.randn(200) - 2
x2 = np.random.randn(200)
x3 = np.random.randn(200) + 2

hist_data = [x1, x2, x3]

group_labels = ['Group 1', 'Group 2', 'Group 3']
colors = ['#A56CC1', '#A6ACEC', '#63F5EF']

# Create distplot with curve_type set to 'normal'
fig = ff.create_distplot(hist_data, group_labels, colors=colors,
                         bin_size=.2, show_rug=False)

# Add title
fig.update_layout(title_text='Hist and Curve Plot')
fig.show()
```

效果如下：

<iframe src="//player.bilibili.com/player.html?bvid=BV1nj411u7gG&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

在streamlit中使用只需要简单替换`fig.show()`:

``` python
import streamlit as st
import numpy as np
import plotly.figure_factory as ff

# Add histogram data
x1 = np.random.randn(200) - 2
x2 = np.random.randn(200)
x3 = np.random.randn(200) + 2

# Group data together
hist_data = [x1, x2, x3]

group_labels = ['Group 1', 'Group 2', 'Group 3']
colors = ['#A56CC1', '#A6ACEC', '#63F5EF']

# Create distplot with custom bin_size
fig = ff.create_distplot(hist_data, group_labels, colors=colors,
                         bin_size=.2, show_rug=False)

# Add title
fig.update_layout(title_text='Hist and Curve Plot')

# Plot!
st.plotly_chart(fig, use_container_width=True)
```

我们再运行一次：

<iframe src="//player.bilibili.com/player.html?bvid=BV15v4y1875T&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

ok，现在背景铺垫完了，我们开始正题，画一个股票蜡烛图。

蜡烛图是一种金融图表样式，描述给定坐标的开盘价、最高价、最低价和收盘价（通常是日期）。框表示 开盘价 和 收盘价 之间的分布，线条表示 最高价 和 最低价 之间的分布。收盘价高于开盘价意味着当日上涨，反之则是下跌。默认情况下，上涨的蜡烛以绿色绘制，而下跌的蜡烛以红色绘制，这是美国股市的习惯。而国内市场一般是以红色代表上涨，绿色代表下跌。

plotly中提供了[蜡烛图](https://plotly.com/python/candlestick-charts/)方法，可以非常方便快速的绘制蜡烛图。

来一段最简代码：

``` python
import plotly.graph_objects as go

import pandas as pd
from datetime import datetime

df = pd.read_csv('https://raw.githubusercontent.com/plotly/datasets/master/finance-charts-apple.csv')

fig = go.Figure(data=[go.Candlestick(x=df['Date'],
                open=df['AAPL.Open'],
                high=df['AAPL.High'],
                low=df['AAPL.Low'],
                close=df['AAPL.Close'])])

fig.show()
```

这段代码读取github上存放的苹果公司的股票历史数据，并画出蜡烛图。

运行一下：

<iframe src="//player.bilibili.com/player.html?bvid=BV1nM4y1C7jH&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

现在我们结合昨天的上传文件插件，编写一个通过读取csv文件来显示股票蜡烛图的程序：

``` python
"""
    上传CSV，绘制蜡烛图
"""

import numpy as np
import pandas as pd

import streamlit as st
import plotly.graph_objects as go

st.title('st.file_uploader')

st.subheader('Input CSV')
uploaded_file = st.file_uploader("Choose a file")

if uploaded_file is not None:
    df = pd.read_csv(uploaded_file, thousands=",")
    st.subheader('DataFrame')
    st.write(df)
    st.subheader('Descriptive Statistics')
    st.write(df.describe())

    fig = go.Figure(data=go.Candlestick(
        x=df['日期'],
        open=(df['开盘']),
        high=df['高'],
        low=df['低'],
        close=df['收盘']))
    st.plotly_chart(fig, use_container_width=True)
else:
    st.info('☝️ Upload a CSV file')
```

`沪深300指数历史数据.csv`示例文件[下载](https://blog.boringhex.top/assets/%E6%B2%AA%E6%B7%B1300%E6%8C%87%E6%95%B0%E5%8E%86%E5%8F%B2%E6%95%B0%E6%8D%AE.csv)。

上面的代码已经按照国内市场的习惯，红涨绿跌，运行一下：

<iframe src="//player.bilibili.com/player.html?bvid=BV1mY411B7A7&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
