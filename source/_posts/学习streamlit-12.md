---
title: 学习streamlit-12
date: 2023-03-15 12:03:01
categories:
tags:
  - python
  - streamlit
---

## [streamlit页面布局](https://docs.streamlit.io/library/api-reference/layout)

今天学习streamlit网页应用的布局，在快速演示程序中，涉及到以下几个streamlit对象：

- `st.set_page_config(layout="wide")`
  以宽模式显示app内容，不指定的话，默认以固定宽度显示内容。

- `st.sidebar`
  放置边栏组件，或在边栏显示文本和图片

- `st.expander`
  在一个下拉容器中显示文本或图片

- `st.columns`
  创建表栏式空间、列表空间

<!-- more -->

运行效果：

<iframe src="//player.bilibili.com/player.html?bvid=BV1dL411k7ph&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

代码：

``` python
import streamlit as st

st.set_page_config(layout="wide")

st.title('How to layout your Streamlit app')

with st.expander('About this app'):
  st.write('This app shows the various ways on how you can layout your Streamlit app.')
  st.image('https://streamlit.io/images/brand/streamlit-logo-secondary-colormark-darktext.png', width=250)

st.sidebar.header('Input')
user_name = st.sidebar.text_input('What is your name?')
user_emoji = st.sidebar.selectbox('Choose an emoji', ['', '😄', '😆', '😊', '😍', '😴', '😕', '😱'])
user_food = st.sidebar.selectbox('What is your favorite food?', ['', 'Tom Yum Kung', 'Burrito', 'Lasagna', 'Hamburger', 'Pizza'])

st.header('Output')

col1, col2, col3 = st.columns(3)

with col1:
  if user_name != '':
    st.write(f'👋 Hello {user_name}!')
  else:
    st.write('👈  Please enter your **name**!')

with col2:
  if user_emoji != '':
    st.write(f'{user_emoji} is your favorite **emoji**!')
  else:
    st.write('👈 Please choose an **emoji**!')

with col3:
  if user_food != '':
    st.write(f'🍴 **{user_food}** is your favorite **food**!')
  else:
    st.write('👈 Please choose your favorite **food**!')
```

streamlit现在支持以下几种页面布局：

![](https://imgs.boringhex.top/blog/20230315164203.png)

### [Sidebar](https://docs.streamlit.io/library/api-reference/layout/st.sidebar)

在streamlit中，我们不仅可以通过组件增强交互性，还可以把内容组织到侧边栏中。可以使用对象赋值或`with`语法将元素传递给`st.sidebar`。以下两种表示方法是等效的：

``` python
# 1. Object notation
st.sidebar.[element_name]

# 2. "with" notation
with st.sidebar:
    st.[element_name]
```

传递给`st.sidebar`的每一个元素会被贴到左侧边栏，以使用户可以重点关注页面主体内容。侧边栏尺寸是可变的，可以通过拖拽侧边栏的边缘调整它的宽度。

再来复习下之前的`st.selectbox`和`st.radio`，我们把它放在侧边栏：

``` python
import streamlit as st

# Using object notation
add_selectbox = st.sidebar.selectbox(
    "How would you like to be contacted?",
    ("Email", "Home phone", "Mobile phone")
)

# Using "with" notation
with st.sidebar:
    add_radio = st.radio(
        "Choose a shipping method",
        ("Standard (5-15 days)", "Express (2-5 days)")
    )
```

运行一下：

<iframe src="//player.bilibili.com/player.html?bvid=BV1M24y1u7kJ&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

需要注意的是，`st.echo` 和 `st.spinner` 元素现在不支持对象赋值传递到侧边栏，要使用的话必须用`with`语法：

``` python
import streamlit as st

with st.sidebar:
    with st.echo():
        st.write("This code will be printed to the sidebar.")

    with st.spinner("Loading..."):
        time.sleep(5)
    st.success("Done!")
```

### [Columns](https://docs.streamlit.io/library/api-reference/layout/st.columns)

栏目式布局，通常用于并列关系的直观表达。与侧边栏类似，可以用`with`语法和对象赋值来创建和使用栏目。栏目最多支持1层嵌套，同时需要注意，侧边栏中的分栏不支持嵌套。

下面来看两个具体例子：

``` python
import streamlit as st

col1, col2, col3 = st.columns(3)

with col1:
   st.header("A cat")
   st.image("https://static.streamlit.io/examples/cat.jpg")

with col2:
   st.header("A dog")
   st.image("https://static.streamlit.io/examples/dog.jpg")

with col3:
   st.header("An owl")
   st.image("https://static.streamlit.io/examples/owl.jpg")
```

运行效果：

<iframe loading="lazy" src="https://doc-columns1.streamlit.app/?embed=true" height="400" width="100%"></iframe>

``` python
import streamlit as st
import numpy as np

col1, col2 = st.columns([3, 1])
data = np.random.randn(10, 1)

col1.subheader("A wide column with a chart")
col1.line_chart(data)

col2.subheader("A narrow column with the data")
col2.write(data)
```

运行效果：

<iframe loading="lazy" src="https://doc-columns2.streamlitapp.com/?embed=true" height="400" width="100%"></iframe>

### [Tabs](https://docs.streamlit.io/library/api-reference/layout/st.tabs)

`st.tabs`是元素的表栏布局模式，分栏是同时并列显示，表栏则是标签页的形式。还是以两段代码来进行演示：

示例1：

``` python
import streamlit as st

tab1, tab2, tab3 = st.tabs(["Cat", "Dog", "Owl"])

with tab1:
   st.header("A cat")
   st.image("https://static.streamlit.io/examples/cat.jpg", width=200)

with tab2:
   st.header("A dog")
   st.image("https://static.streamlit.io/examples/dog.jpg", width=200)

with tab3:
   st.header("An owl")
   st.image("https://static.streamlit.io/examples/owl.jpg", width=200)
```

运行效果：

<iframe loading="lazy" src="https://doc-tabs1.streamlitapp.com/?embed=true" height="400" width="100%"></iframe>

示例2：

``` python
import streamlit as st
import numpy as np

tab1, tab2 = st.tabs(["📈 Chart", "🗃 Data"])
data = np.random.randn(10, 1)

tab1.subheader("A tab with a chart")
tab1.line_chart(data)

tab2.subheader("A tab with the data")
tab2.write(data)
```

运行效果：

<iframe loading="lazy" src="https://doc-tabs2.streamlitapp.com/?embed=true" height="400" width="100%"></iframe>

### [Expander](https://docs.streamlit.io/library/api-reference/layout/st.expander)

`st.expander`可以用来创建一个折叠/展开容器，里边可以放入多种元素，使用方法也是`with`语句或对象赋值，来看以下两个代码示例：

示例1：

``` python
import streamlit as st

st.bar_chart({"data": [1, 5, 2, 6, 2, 1]})

with st.expander("See explanation"):
    st.write(\"\"\"
        The chart above shows some numbers I picked for you.
        I rolled actual dice for these, so they're *guaranteed* to
        be random.
    \"\"\")
    st.image("https://static.streamlit.io/examples/dice.jpg")
```

运行效果：

<iframe loading="lazy" src="https://doc-expander.streamlitapp.com/?embed=true" height="400" width="100%"></iframe>

示例2：

``` python
import streamlit as st

st.bar_chart({"data": [1, 5, 2, 6, 2, 1]})

expander = st.expander("See explanation")
expander.write(\"\"\"
    The chart above shows some numbers I picked for you.
    I rolled actual dice for these, so they're *guaranteed* to
    be random.
\"\"\")
expander.image("https://static.streamlit.io/examples/dice.jpg")
```

运行效果：

<iframe loading="lazy" src="https://doc-expander.streamlitapp.com/?embed=true" height="400" width="100%"></iframe>

### [Container](https://docs.streamlit.io/library/api-reference/layout/st.container)

`st.container`用于创建嵌入容器，这个容器的边缘不可见，可以用容器存放多种元素而无需考虑顺序。下面是两段示例代码：

示例1：

``` python
import streamlit as st

with st.container():
   st.write("This is inside the container")

   # You can call any Streamlit command, including custom components:
   st.bar_chart(np.random.randn(50, 3))

st.write("This is outside the container")
```

运行效果：

<iframe loading="lazy" src="https://doc-container1.streamlitapp.com/?embed=true" height="400" width="100%"></iframe>

示例2：

``` python
import streamlit as st

container = st.container()
container.write("This is inside the container")
st.write("This is outside the container")

# Now insert some more in the container
container.write("This is inside too")
```

运行效果：

<iframe loading="lazy" src="https://doc-container2.streamlitapp.com/?embed=true" height="400" width="100%"></iframe>

### [Empty](https://docs.streamlit.io/library/api-reference/layout/st.empty)

`st.empty`用来创建空容器，它的作用有点像占位符，可以用来保持一个元素，也可以在任一点一次性替换或移除元素，来看下面两个代码示例：

示例1：

``` python
import streamlit as st
import time

with st.empty():
    for seconds in range(60):
        st.write(f"⏳ {seconds} seconds have passed")
        time.sleep(1)
    st.write("✔️ 1 minute over!")
```

这段代码创建了一个计时器，从10s开始倒计时。如果不用`st.empty`，则会输出10条计时信息，用空容器则会在原地更新计时信息。

示例2：

``` python
import streamlit as st

placeholder = st.empty()

# Replace the placeholder with some text:
placeholder.text("Hello")

# Replace the text with a chart:
placeholder.line_chart({"data": [1, 5, 2, 6]})

# Replace the chart with several elements:
with placeholder.container():
    st.write("This is one element")
    st.write("This is another")

# Clear all those elements:
placeholder.empty()
```

这段代码演示了替换和清除，每执行一行，容器内容会被更新，直到最后清空。

运行效果：

<iframe src="//player.bilibili.com/player.html?bvid=BV1Hs4y1V76o&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
