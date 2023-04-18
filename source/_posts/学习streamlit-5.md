---
title: 学习streamlit-5
date: 2023-02-28 09:58:12
categories:
tags:
  - python
  - streamlit
---

## [`st.line_chart`](https://docs.streamlit.io/library/api-reference/charts/st.line_chart)

今天继续学习streamlit，`st.line_chart`组件的使用。

`st.line_chart`用来显示折线图，它是`st.altair_chart`上的语法糖。主要区别在于此命令使用数据自己的列和索引来计算图表的规格。因此，这更容易用于许多“只是绘制此内容”方案，同时可定制性较低。

如果无法正确猜测数据特征，请尝试使用 `st.altair_chart` 指定所需的图表。

<!-- more -->

今天要构建的应用演示了以下流程：

1. 通过pandas、numpy生成随机数组成的数据帧。
2. 用折线图显示生成的数据帧。

### demo

点击下方按钮进行在线演示和交互：

[![Open in Streamlit](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://share.streamlit.io/dataprofessor/st.line_chart/)

显示效果：

![](https://imgs.boringhex.top/blog/20230228102946.png)

### 代码

``` python
import streamlit as st
import pandas as pd
import numpy as np

st.header('Line chart')

chart_data = pd.DataFrame(
     np.random.randn(20, 3),
     columns=['a', 'b', 'c'])

st.line_chart(chart_data)
```

逐行解释下上面的代码：

首先要做的依然是导入用到的python库：

``` python
import streamlit as st
import pandas as pd
import numpy as np
```

然后为图表创建标题：

``` python
st.header('Line chart')
```

生成一个包含3列20行随机数的数据帧并赋值给`chart_data`：

``` python
chart_data = pd.DataFrame(
     np.random.randn(20, 3),
     columns=['a', 'b', 'c'])
```

最后，用折线图绘制数据：

``` python
st.line_chart(chart_data)
```

每次运行折线图都会有所不同，因为数据是随机生成的。

## [`st.altair_chart`](https://docs.streamlit.io/library/api-reference/charts/st.altair_chart)

接下来进一步学习下`st.line_chart`背后的`st.altair_chart`组件，使用Altair库绘制各种图表。

作为简单示例，我们用与前文相同的方法生成数据，并以散点图的形式显示：

### demo

点击下方按钮进行在线演示：

[![Open in Streamlit](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://doc-vega-lite-chart.streamlit.app/)

运行效果：

![](https://imgs.boringhex.top/blog/visualization.svg)

### 代码

``` python
import streamlit as st
import pandas as pd
import numpy as np
import altair as alt

chart_data = pd.DataFrame(
    np.random.randn(20, 3),
    columns=['a', 'b', 'c'])

c = alt.Chart(chart_data).mark_circle().encode(
    x='a', y='b', size='c', color='c', tooltip=['a', 'b', 'c'])

st.altair_chart(c, use_container_width=True)
```

首先导入了必要的库：Streamlit、Pandas、NumPy、Altair。然后使用 Pandas 生成一个 DataFrame，包含 20 行、3 列随机数。

接下来使用 Altair 创建了一个散点图，图中横轴为 DataFrame 的第一列（'a'），纵轴为第二列（'b'），点的大小和颜色都由第三列（'c'）的值确定，鼠标悬停在图上时会显示三列数据的具体数值。

最后使用 Streamlit 的 `altair_chart` 函数将图表展示在网页中，`use_container_width=True` 参数将图表宽度自适应网页宽度。用户可以使用 Streamlit 运行这个应用，并在页面上交互地探索数据。

### 主题

默认情况下，Altair 图表使用streamlit主题显示。这个主题时尚、用户友好，并结合了 Streamlit 的调色板。额外的好处是，你的图表可以更好地与应用的其余部分集成。

Streamlit 主题从 Streamlit 1.16.0 开始可以通过关键字参数获得。要禁用它并使用 Altair 的原生主题，请修改`theme="streamlit"`为`theme=None`。

让我们看一个具有 Streamlit 主题和原生 Altair 主题的图表示例，可以点击下面交互式应用程序的选项卡切换主题：

<iframe loading="lazy" src="https://doc-altair-chart.streamlit.app/?embed=true" height="500" width="100%"></iframe>

默认streamlit主题：

![streamlit默认主题图表](https://imgs.boringhex.top/blog/202304182255903.png)

原生altair主题：

![altair主题图表](https://imgs.boringhex.top/blog/202304182257583.png)

[了解更多](https://altair-viz.github.io/gallery/)Altair图表示例。

以上示例的代码：

``` python
import altair as alt
from vega_datasets import data

source = data.cars()

chart = alt.Chart(source).mark_circle().encode(
    x='Horsepower',
    y='Miles_per_Gallon',
    color='Origin',
).interactive()

tab1, tab2 = st.tabs(["Streamlit theme (default)", "Altair native theme"])

with tab1:
    # Use the Streamlit theme.
    # This is the default. So you can also omit the theme argument.
    st.altair_chart(chart, theme="streamlit", use_container_width=True)
with tab2:
    # Use the native Altair theme.
    st.altair_chart(chart, theme=None, use_container_width=True)
```
