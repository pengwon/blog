---
title: 学习streamlit-4
date: 2023-02-27 09:51:10
categories:
tags:
  - python
  - streamlit
---

## [`st.slider`](https://docs.streamlit.io/library/api-reference/widgets/st.slider)

今天学习`st.slider`滑块组件的使用。

`st.slider`滑块组件通常被用来作为应用的输入，支持整数、浮点数、日期、时间和日期时间。

下面的示例程序包含以下简单功能，以演示`st.slider`滑块组件：

1. 用户通过调整滑块选择值
2. 应用打印出所选值

<!-- more -->

代码：

``` python
import streamlit as st
from datetime import time, datetime

st.header('st.slider')

# Example 1

st.subheader('Slider')

age = st.slider('How old are you?', 0, 130, 25)
st.write("I'm ", age, 'years old')

# Example 2

st.subheader('Range slider')

values = st.slider(
     'Select a range of values',
     0.0, 100.0, (25.0, 75.0))
st.write('Values:', values)

# Example 3

st.subheader('Range time slider')

appointment = st.slider(
     "Schedule your appointment:",
     value=(time(11, 30), time(12, 45)))
st.write("You're scheduled for:", appointment)

# Example 4

st.subheader('Datetime slider')

start_time = st.slider(
     "When do you start?",
     value=datetime(2020, 1, 1, 9, 30),
     format="MM/DD/YY - hh:mm")
st.write("Start time:", start_time)
```

运行后效果：

![](https://imgs.boringhex.top/blog/20230227101745.png)

<iframe src="//player.bilibili.com/player.html?bvid=BV1cX4y1X7pm&page=1&quality=high" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

可以[在线演示](https://share.streamlit.io/dataprofessor/st.slider/)或点击下方按钮，在网页与应用交互，实时查看效果。

[![Open in Streamlit](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://share.streamlit.io/dataprofessor/st.slider/)

### 逐行解释

创建streamlit应用时要做的第一件事就是导入库：

``` python
import streamlit as st
from datetime import time, datetime
```

然后创建一个文本标题：

``` python
st.header('st.slider')
```

**例1**

滑块：

``` python
st.subheader('Slider')

age = st.slider('How old are you?', 0, 130, 25)
st.write("I'm ", age, 'years old')
```

正如我们所见，滑块在这里用来收集有关用户年龄的输入，第一个参数是显示在滑块组件上方的询问文本"How old are you?"，之后三个整数分别表示滑块的最小值、最大值和默认值0、130、25.

**例2**

范围滑块：

``` python
st.subheader('Range slider')

values = st.slider(
     'Select a range of values',
     0.0, 100.0, (25.0, 75.0))
st.write('Values:', values)
```

范围滑块允许选择下限值和上限值对。

第一个输入参数显示范围滑块小部件正上方询问 的文本。'Select a range of values'

以下三个参数表示最小值和最大值，而最后一个元组表示要用作所选下限 （25.0） 和上限 （75.0） 边界值的默认值。0.0, 100.0, (25.0, 75.0)

**例3**

范围时间滑块：

``` python
st.subheader('Range time slider')

appointment = st.slider(
     "Schedule your appointment:",
     value=(time(11, 30), time(12, 45)))
st.write("You're scheduled for:", appointment)
```

范围时间滑块允许选择日期时间的下限值和上限值对。

第一个输入参数显示范围时间滑块小部件正上方的文本，询问'Schedule your appointment'

日期时间的下限值和上限值对的默认值分别设置为 11：30 和 12：45。

**例4**

日期时间滑块：

``` python
st.subheader('Datetime slider')

start_time = st.slider(
     "When do you start?",
     value=datetime(2020, 1, 1, 9, 30),
     format="MM/DD/YY - hh:mm")
st.write("Start time:", start_time)
```

日期时间滑块允许选择日期时间。

第一个输入参数显示日期时间滑块小部件正上方的文本，询问'When do you start?'

通过`value`参数把日期时间的默认值设置为'2020年1月1日9:30'。

## [`st.select_slider`](https://docs.streamlit.io/library/api-reference/widgets/st.select_slider)

streamlit还提供了选择滑块组件`st.select_slider`，用法与`st.slider`非常相似。

示例代码：

``` python
import streamlit as st

color = st.select_slider(
    'Select a color of the rainbow',
    options=['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'])
st.write('My favorite color is', color)

start_color, end_color = st.select_slider(
    'Select a range of color wavelength',
    options=['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'],
    value=('red', 'blue'))
st.write('You selected wavelengths between', start_color, 'and', end_color)
```

运行效果：

![](https://imgs.boringhex.top/blog/20230227151819.png)

<iframe src="//player.bilibili.com/player.html?bvid=BV1RT411e7oR&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
