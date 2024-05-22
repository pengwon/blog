---
title: 学习streamlit-2
date: 2023-02-23 14:01:38
categories:
tags:
  - python
  - streamlit
---

首先视频快速预览下今天的学习内容：

<iframe src="//player.bilibili.com/player.html?bvid=BV1W24y1n7xC&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

<!-- more -->

今天继续学习streamlit，首先激活之前建立的虚拟环境：

``` powershell
❯ conda activate streamlit-env
(streamlit-env)
~ via 🐍 v3.9.16 via 🅒 streamlit-env
❯ 
```

进入到虚拟环境目录下，新建`exercises`文件夹，新建`day1.py`文件：

``` powershell
❯ fd streamlit-env
anaconda3\envs\streamlit-env

❯ fd streamlit-env | cd

❯ mkdir exercises

❯ cd exercises

❯ code day1.py
```

在`day1.py`文件中写下第一行代码：

``` python
import streamlit as st

st.write('Hello world!')
```

保存，然后在终端中运行：

``` powershell
❯ streamlit run .\day1.py

  You can now view your Streamlit app in your browser.

  Local URL: http://localhost:8501
  Network URL: http://192.168.20.81:8501
```

自动打开系统默认浏览器窗口并显示 "Hello world!"

恭喜，第一个程序运行起来了！

我们现在给页面加个按钮，实现点按钮后变换显示内容：

``` python
# 在day1.py文件中继续编辑，并且不需要关闭之前运行的程序
import streamlit as st

st.write('Hello world!')

st.header('st.button')

if st.button('Say hello'):
    st.write('Why hello there')
else:
    st.write('Goodbye')
```

刷新下网页，会出现按钮：

![](https://imgs.boringhex.top/blog/msedge_S8a0SBdqqx.gif)

上面演示了`st.button`最简单的用法，点击[了解更多](https://docs.streamlit.io/library/api-reference/widgets/st.button)。

``` python
st.button(label, key=None, help=None, on_click=None, args=None, kwargs=None, *, type="secondary", disabled=False, use_container_width=False)
```

### Parameters

- label(str)

  按钮标签，通常用字符串，支持markdown语法的加粗、斜体、删除线，还支持emojis。

- key(str or int)

  可选的字符串或整型作为按钮部件的唯一键值，如果留空，则自动根据按键内容生成。

- help(str)

  可选的当鼠标悬浮在按键上时显示的提示信息。

- on_click(callable)

  可选的点击按钮的回调。

- args(tuple)

  可选的传给回调函数的元组参数。

- kwargs(dict)

  可选的传给回调函数的字典参数。

- type("secondary" or "primary")

  可选的指定按钮类型的参数，默认为"secondary"，为普通类型；"primary"则会着重强调。

- disable(bool)

  可选的布尔类型参数，默认为`False`，设为`True`则会禁用按钮。

- use_container_width(bool)

  可选的布尔类型参数，控制按钮是否匹配父容器宽度。

### Returns

- (bool)

  程序在最后一次运行时按钮被点击则返回`True`，否则为`False`。

## Demo

![](https://imgs.boringhex.top/blog/msedge_5nAHlgQspC.gif)

代码如下：

``` python
import streamlit as st
import numpy as np
import time


st.write('Hello world!')

st.header('st.button')

if st.button('Say hello'):
    st.write('Why hello there')
else:
    st.write('Goodbye')

st.button('**你好**')

st.button('*你好*')

st.button('~哈哈~')

st.button('❤')

st.button('❤', key='1', help='This is a heart emoji button')

def plotting_demo():
    progress_bar = st.sidebar.progress(0)
    status_text = st.sidebar.empty()
    last_rows = np.random.randn(1, 1)
    chart = st.line_chart(last_rows)

    for i in range(1, 101):
        new_rows = last_rows[-1, :] + np.random.randn(5, 1).cumsum(axis=0)
        status_text.text("%i%% Complete" % i)
        chart.add_rows(new_rows)
        progress_bar.progress(i)
        last_rows = new_rows
        time.sleep(0.05)

    progress_bar.empty()

    # Streamlit widgets automatically run the script from top to bottom. Since
    # this button is not connected to any other logic, it just causes a plain
    # rerun.
    st.button("Re-run")

plotting_demo()
```
