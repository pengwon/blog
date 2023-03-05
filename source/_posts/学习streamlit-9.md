---
title: 学习streamlit-9
date: 2023-03-05 21:08:47
categories:
tags:
  - python
  - streamlit
---

## `st.secrets`

许多 Streamlit 应用需要访问私有数据，例如 API 密钥、数据库密码或其他凭据。为了确保数据安全，最佳做法是切勿将此类凭据直接存储在托管存储库中。

`st.secrets`允许开发者存储机密信息，例如api密钥、数据库密码或其他凭据。

### demo

点击按钮运行demo程序：

[![Open in Streamlit](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://share.streamlit.io/dataprofessor/st.secrets/)

<!-- more -->

### 代码

``` python
import streamlit as st

st.title('st.secrets')

st.write(st.secrets['message'])
```

需要注意的是，上面的应用中机密信息是存储在streamlit社区云中。如果在本地运行，则需要把机密信息存储在`.streamlit/secrets.toml`文件中，并且一定要注意**不要将该文件上传到GitHub或其它公开仓库中**，**不要将该文件上传到GitHub或其它公开仓库中**，**不要将该文件上传到GitHub或其它公开仓库中**。

## streamlit社区云机密管理

托管在streamlit社区云上的应用可以非常方便的使用由社区云提供的机密管理。

### 部署应用并设置机密信息

首先要到 http://share.streamlit.io/ ，点击 New app 新建一个app，接下来点击 Advanced settings 将会出现一个对话框，在这个对话框中可以看到用于添加机密信息的输入框：

![](https://blog.streamlit.io/content/images/2021/08/gif-1-4.gif)

以[`TOML`](https://toml.io/en/latest?ref=streamlit)格式在输入框中添加机密信息，例如：

``` toml
# Everything in this section will be available as an environment variable 
db_username = "Jane"
db_password = "12345qwerty"

# You can also add other sections if you like.
# The contents of sections as shown below will not become environment variables,
# but they'll be easily accessible from within Streamlit anyway as we show
# later in this doc.   
[my_cool_secrets]
things_i_like = ["Streamlit", "Python"]
```

点击保存，机密信息就被添加到应用运行环境中了。

### 在应用中使用机密信息

若要在应用中使用机密信息，只需要将其作为环境变量或通过查询字典来访问。例如，对于我们上面输入的机密信息，可以在streamlit应用中通过以下代码进行访问：

``` python
import streamlit as st

# Everything is accessible via the st.secrets dict:

st.write("DB username:", st.secrets["db_username"])
st.write("DB password:", st.secrets["db_password"])
st.write("My cool secrets:", st.secrets["my_cool_secrets"]["things_i_like"])

# And the root-level secrets are also accessible as environment variables:

import os
st.write(
	"Has environment variables been set:",
	os.environ["db_username"] == st.secrets["db_username"])
```

### 编辑机密信息

在已部署的streamlit应用中添加或更新机密信息也非常简单，只需要：

1. 访问 https://share.streamlit.io/ 。
2. 打开需要更新的应用的菜单，然后点击 Edit secrets，将出现一个对话框，在其中编辑机密信息即可。
   ![](https://blog.streamlit.io/content/images/2021/08/4.1-1.png)
3. 编辑完成后，点击保存。更新可能需要几秒钟才会被部署到应用中，并且需要重新运行应用才会同步到新的值。
