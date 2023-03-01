---
title: 学习streamlit-3
date: 2023-02-24 10:05:39
categories:
tags:
  - python
  - streamlit
---

前两篇文章中一直在使用streamlit的方法`st.write()`来输出文本和参数到页面。

除了文本和参数，`st.write()`还支持以下内容的显示：

- 打印字符串，与`st.markdown()`方法相似。
- 显示python字典。
- 显示`pandas`数据帧，以表格的样式。
- 画图，来源可以是`matplotlib`, `plotly`, `altair`, `graphviz`, `bokeh`。
- [更多特性](https://docs.streamlit.io/library/api-reference/write-magic/st.write)还在添加。

<!-- more -->

## [`st.write`](https://docs.streamlit.io/library/api-reference/write-magic/st.write)

下面用一段代码来演示`st.write()`显示各种内容：

``` python
import numpy as np
import altair as alt
import pandas as pd
import streamlit as st

st.header('st.write')

# Example 1

st.write('Hello, *World!* :sunglasses:')

# Example 2

st.write(1234)

# Example 3

df = pd.DataFrame({
     'first column': [1, 2, 3, 4],
     'second column': [10, 20, 30, 40]
     })
st.write(df)

# Example 4

st.write('Below is a DataFrame:', df, 'Above is a dataframe.')

# Example 5

df2 = pd.DataFrame(
     np.random.randn(200, 3),
     columns=['a', 'b', 'c'])
c = alt.Chart(df2).mark_circle().encode(
     x='a', y='b', size='c', color='c', tooltip=['a', 'b', 'c'])
st.write(c)
```

运行后显示效果：

![](https://imgs.boringhex.top/blog/20230224104825.png)

除了`st.write()`方法，还可以使用下面几个方法来显示内容：

## [`st.markdown`](https://docs.streamlit.io/library/api-reference/text/st.markdown)

``` python
import streamlit as st

st.markdown('Streamlit is **_really_ cool**.')
st.markdown(”This text is :red[colored red], and this is **:blue[colored]** and bold.”)
st.markdown(":green[$\sqrt{x^2+y^2}=1$] is a Pythagorean identity. :pencil:")
```

显示效果：

![](https://imgs.boringhex.top/blog/20230224110414.png)

## [`st.title`](https://docs.streamlit.io/library/api-reference/text/st.title)

``` python
import streamlit as st

st.title('This is a title')
st.title('A title with _italics_ :blue[colors] and emojis :sunglasses:')
```

显示效果：

![](https://imgs.boringhex.top/blog/20230224110656.png)

## [`st.header`](https://docs.streamlit.io/library/api-reference/text/st.header)

``` python
import streamlit as st

st.header('This is a header')
st.header('A header with _italics_ :blue[colors] and emojis :sunglasses:')
```

显示效果：

![](https://imgs.boringhex.top/blog/20230224110928.png)

## [`st.subheader`](https://docs.streamlit.io/library/api-reference/text/st.subheader)

``` python
import streamlit as st

st.subheader('This is a subheader')
st.subheader('A subheader with _italics_ :blue[colors] and emojis :sunglasses:')
```

显示效果：

![](https://imgs.boringhex.top/blog/20230224111133.png)

## [`st.caption`](https://docs.streamlit.io/library/api-reference/text/st.caption)

``` python
import streamlit as st

st.caption('This is a string that explains something above.')
st.caption('A caption with _italics_ :blue[colors] and emojis :sunglasses:')
```

显示效果：

![](https://imgs.boringhex.top/blog/20230224111355.png)

## [`st.code`](https://docs.streamlit.io/library/api-reference/text/st.code)

``` python
import streamlit as st

code = '''def hello():
    print("Hello, Streamlit!")'''
st.code(code, language='python')
```

显示效果：

![](https://imgs.boringhex.top/blog/20230224111655.png)

## [`st.text`](https://docs.streamlit.io/library/api-reference/text/st.text)

``` python
import streamlit as st

st.text('This is some text.')
```

显示效果：

![](https://imgs.boringhex.top/blog/20230224111844.png)

## [`st.latex`](https://docs.streamlit.io/library/api-reference/text/st.latex)

``` python
import streamlit as st

st.latex(r'''
    a + ar + a r^2 + a r^3 + \cdots + a r^{n-1} =
    \sum_{k=0}^{n-1} ar^k =
    a \left(\frac{1-r^{n}}{1-r}\right)
    ''')
```

显示效果：

![](https://imgs.boringhex.top/blog/20230224112141.png)

## [magic](https://docs.streamlit.io/library/api-reference/write-magic/magic)

streamlit中还有一个"magic"命令，它可以让我们几乎显示任何东西，markdown、数据、图表等等，并且无需输入任何显式命令，只输入要显示的代码行本身就可以显示，比如以下代码：

``` python
# Draw a title and some text to the app:
'''
# This is the document title

This is some _markdown_.
'''

import pandas as pd
df = pd.DataFrame({'col1': [1,2,3]})
df  # 👈 Draw the dataframe

x = 10
'x', x  # 👈 Draw the string 'x' and then the value of x

# Also works with most supported chart types
import matplotlib.pyplot as plt
import numpy as np

arr = np.random.normal(1, 1, size=100)
fig, ax = plt.subplots()
ax.hist(arr, bins=20)

fig  # 👈 Draw a Matplotlib chart
```

运行后效果：

![](https://imgs.boringhex.top/blog/20230224115106.png)

视频教程：

<iframe src="//player.bilibili.com/player.html?bvid=BV1sT411Y7pT&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>
