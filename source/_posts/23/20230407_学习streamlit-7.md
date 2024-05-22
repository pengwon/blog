---
title: 学习streamlit-7
date: 2023-03-03 15:35:28
categories:
tags:
  - python
  - streamlit
---

今天继续学习streamlit中其它的选择方式。

## [`st.multiselect`](https://docs.streamlit.io/library/api-reference/widgets/st.multiselect)

与selectbox的用法非常相似，只是选项变成了多选。接下来，我们看一下demo程序：

<iframe loading="lazy" src="https://share.streamlit.io/dataprofessor/st.multiselect/?embed=true" height="400" width="100%"></iframe>

<!-- more -->

使用演示：

![](https://imgs.boringhex.top/blog/streamlit-streamlit_app-2023-03-03-15-03-45.gif)

示例程序给出选项列表，允许用户从中选择多种颜色，并打印出来。

### 代码

``` python
import streamlit as st

st.header('st.multiselect')

options = st.multiselect(
     'What are your favorite colors',
     ['Green', 'Yellow', 'Red', 'Blue'],
     ['Yellow', 'Red'])

st.write('You selected:', options)
```

## [`st.checkbox`](https://docs.streamlit.io/library/api-reference/widgets/st.checkbox)

除了选项列表外，还可以通过checkbox进行多选：

<iframe loading="lazy" src="https://share.streamlit.io/dataprofessor/st.checkbox/?embed=true" height="400" width="100%"></iframe>

使用演示：

![](https://imgs.boringhex.top/blog/streamlit-streamlit_app-2023-03-03-16-03-63.gif)

### 代码

``` python
import streamlit as st

st.header('st.checkbox')

st.write ('What would you like to order?')

icecream = st.checkbox('Ice cream')
coffee = st.checkbox('Coffee')
cola = st.checkbox('Cola')

if icecream:
     st.write("Great! Here's some more 🍦")

if coffee: 
     st.write("Okay, here's some coffee ☕")

if cola:
     st.write("Here you go 🥤")
```

这段代码创建一个交互式应用，用于显示选项，用户可以勾选他们想要的选项。

首先，导入 Streamlit 库。接下来，使用 `st.header()` 函数创建一个标题，标题文本为 'st.checkbox'。然后，使用 `st.write()` 函数创建一个简单的文本输出，文本为 'What would you like to order?'。

然后，使用 `st.checkbox()` 函数分别创建三个勾选框，对应了三种不同的饮料：冰淇淋、咖啡和可乐。用户可以在这三个选项中勾选他们想要的饮料。

最后，使用条件语句来检查用户勾选的选项，并使用 `st.write()` 函数根据用户的选择输出相应的文本。如果用户勾选了 'Ice cream'，则会输出 "Great! Here's some more 🍦"；如果用户勾选了 'Coffee'，则会输出 "Okay, here's some coffee ☕"；如果用户勾选了 'Cola'，则会输出 "Here you go 🥤"。

再来练习一个大家在平时最常遇到的情形：

<iframe loading="lazy" src="https://doc-checkbox.streamlitapp.com/?embed=true" height="400" width="100%"></iframe>

代码：

``` python
import streamlit as st

agree = st.checkbox('I agree')

if agree:
    st.write('Great!')
```

最后通过视频再回顾下`st.checkbox`的使用。

<iframe src="//player.bilibili.com/player.html?bvid=BV1jb411Q7Qq&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
