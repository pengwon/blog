---
title: 学习streamlit-6
date: 2023-03-01 14:16:05
categories:
tags:
  - python
  - streamlit
---

## [`st.selectbox`](https://docs.streamlit.io/library/api-reference/widgets/st.selectbox)

今天学习streamlit中`st.selectbox`的使用。

`st.selectbox`是一个用来显示选择的组件，示例程序会问用户最喜欢的颜色：

1. 用户选择一个颜色
2. 打印输出选择的颜色

### 示例

<iframe loading="lazy" src="https://share.streamlit.io/dataprofessor/st.selectbox/?embed=true" height="400" width="100%"></iframe>

<!-- more -->

### 代码

``` python
import streamlit as st

st.header('st.selectbox')

option = st.selectbox(
     'What is your favorite color?',
     ('Blue', 'Red', 'Green'))

st.write('Your favorite color is ', option)
```

这段代码非常好理解，首先还是引入用到的库，然后创建一个标题：

``` python
import streamlit as st

st.header('st.selectbox')
```

接下来是今天学习的主角，我们创建了一个`option`变量，并把`st.selectbox()`获得的选择值传递给这个变量：

``` python
option = st.selectbox(
     'What is your favorite color?',
     ('Blue', 'Red', 'Green'))
```

如我们所见，`st.selectbox()`有两个输入参数：

1. 要在选择组件上显示的文本，在这个例子中是`'What is your favorite color?'`
2. 选项值，在这个例子中是`('Blue', 'Red', 'Green')`

最后，打印出选择值：

``` python
st.write('Your favorite color is ', option)
```

### 练习

创建一个程序，让用户选择练习方式。

效果：

<iframe loading="lazy" src="https://doc-selectbox.streamlitapp.com/?embed=true" height="400" width="100%"></iframe>

代码：

``` python
import streamlit as st

option = st.selectbox(
    'How would you like to be contacted?',
    ('Email', 'Home phone', 'Mobile phone'))

st.write('You selected:', option)
```

### `label_visibility`参数

选择组件可以通过传递`label_visibility`参数来控制组件标签的显示方式，如果把这个参数设为`hidden`，文本标签会隐藏，但是它占用的位置还在，效果与`label=""`相同；如果设为`collapsed`，则标签文本和占用的位置都会移除；这个参数默认值是`visible`。

选择组件还可以通过传递`disabled`参数禁用。

示例：

<iframe loading="lazy" src="https://doc-selectbox1.streamlit.app/?embedded=true" height="400" width="100%"></iframe>

代码：

``` python
import streamlit as st

# Store the initial value of widgets in session state
if "visibility" not in st.session_state:
    st.session_state.visibility = "visible"
    st.session_state.disabled = False

col1, col2 = st.columns(2)

with col1:
    st.checkbox("Disable selectbox widget", key="disabled")
    st.radio(
        "Set selectbox label visibility 👉",
        key="visibility",
        options=["visible", "hidden", "collapsed"],
    )

with col2:
    option = st.selectbox(
        "How would you like to be contacted?",
        ("Email", "Home phone", "Mobile phone"),
        label_visibility=st.session_state.visibility,
        disabled=st.session_state.disabled,
    )
```
