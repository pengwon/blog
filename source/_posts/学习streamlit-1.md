---
title: 学习streamlit-1
date: 2023-02-22 14:46:17
categories:
tags:
  - python
  - streamlit
---

> [Streamlit](https://streamlit.io/)
> A faster way to build and share data apps

streamlit在几分钟内就可以将数据脚本转换为可共享的web应用程序，并且是纯python编程，无需前端经验。

<video muted="" controls="" loop="" class="w-full" __idm_id__="950273"><source src="https://s3-us-west-2.amazonaws.com/assets.streamlit.io/videos/hero-video.mp4"></video>

<!-- more -->

## 快速开始

streamlit非常容易上手，运行demo只需2行代码：

``` python
pip install streamlit
streamlit hello
```

运行后会在系统默认浏览器自动打开页面：

![](https://imgs.boringhex.top/blog/20230222150732.png)

动画demo：

![](https://imgs.boringhex.top/blog/msedge_ElNxHzWfsl.gif)

画曲线图demo：

![](https://imgs.boringhex.top/blog/msedge_eE7elzJNrW.gif)

可以访问[这里](https://streamlit.io/gallery)查看更多好玩儿的demo。

streamlit建立在三个简单原则之上：

- 拥抱脚本
  
  streamlit api非常简洁，正如前面所演示的，使用这些神奇简单的api，只需几行代码就可以构建应用程序，并可以实时更新。

  ![](https://imgs.boringhex.top/blog/20230222152243.png)

- 编织互动

  添加小部件只需声明变量，无需编写后端、定义路由、处理http请求、连接前端、编写html、css、javascript等等

  ![](https://imgs.boringhex.top/blog/20230222152737.png)

- 部署敏捷

  直接从 Streamlit 轻松共享、管理和部署您的应用程序。[完全免费](https://streamlit.io/cloud)！

## 安装

windows上建议使用[anaconda](https://www.anaconda.com/)管理python开发环境，如果嫌弃anaconda过于庞大，可以安装[miniconda](https://docs.conda.io/en/latest/miniconda.html)。

将`conda`命令集成到windows terminal，可以参考[这篇文章](https://blog.boringhex.top/posts/fe1b4febbd53/)。

安装anaconda或miniconda后，建立一个streamlit的开发环境：

``` powershell
❯ conda create -n streamlit-env python=3.9
Collecting package metadata (current_repodata.json): done
Solving environment: done


==> WARNING: A newer version of conda exists. <==
  current version: 4.10.3
  latest version: 23.1.0

Please update conda by running

    $ conda update -n base -c defaults conda



## Package Plan ##

  environment location: C:\Users\yp.wang\anaconda3\envs\streamlit-env

  added / updated specs:
    - python=3.9


The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    ca-certificates-2023.01.10 |       haa95532_0         121 KB  https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
    certifi-2022.12.7          |   py39haa95532_0         149 KB  https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
    openssl-1.1.1t             |       h2bbff1b_0         5.5 MB  https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
    pip-22.3.1                 |   py39haa95532_0         2.7 MB  https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
    python-3.9.16              |       h6244533_0        19.4 MB  https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
    setuptools-65.6.3          |   py39haa95532_0         1.1 MB  https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
    sqlite-3.40.1              |       h2bbff1b_0         889 KB  https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
    tzdata-2022g               |       h04d1e81_0         114 KB  https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
    wheel-0.38.4               |   py39haa95532_0          83 KB  https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
    ------------------------------------------------------------
                                           Total:        30.1 MB

The following NEW packages will be INSTALLED:

  ca-certificates    anaconda/pkgs/main/win-64::ca-certificates-2023.01.10-haa95532_0
  certifi            anaconda/pkgs/main/win-64::certifi-2022.12.7-py39haa95532_0
  openssl            anaconda/pkgs/main/win-64::openssl-1.1.1t-h2bbff1b_0
  pip                anaconda/pkgs/main/win-64::pip-22.3.1-py39haa95532_0
  python             anaconda/pkgs/main/win-64::python-3.9.16-h6244533_0
  setuptools         anaconda/pkgs/main/win-64::setuptools-65.6.3-py39haa95532_0
  sqlite             anaconda/pkgs/main/win-64::sqlite-3.40.1-h2bbff1b_0
  tzdata             anaconda/pkgs/main/noarch::tzdata-2022g-h04d1e81_0
  vc                 anaconda/pkgs/main/win-64::vc-14.2-h21ff451_1
  vs2015_runtime     anaconda/pkgs/main/win-64::vs2015_runtime-14.27.29016-h5e58377_2
  wheel              anaconda/pkgs/main/win-64::wheel-0.38.4-py39haa95532_0
  wincertstore       anaconda/pkgs/main/win-64::wincertstore-0.2-py39haa95532_2


Proceed ([y]/n)?


Downloading and Extracting Packages
python-3.9.16        | 19.4 MB   | ############################################################################ | 100%
pip-22.3.1           | 2.7 MB    | ############################################################################ | 100%
certifi-2022.12.7    | 149 KB    | ############################################################################ | 100%
tzdata-2022g         | 114 KB    | ############################################################################ | 100%
openssl-1.1.1t       | 5.5 MB    | ############################################################################ | 100%
ca-certificates-2023 | 121 KB    | ############################################################################ | 100%
sqlite-3.40.1        | 889 KB    | ############################################################################ | 100%
setuptools-65.6.3    | 1.1 MB    | ############################################################################ | 100%
wheel-0.38.4         | 83 KB     | ############################################################################ | 100%
Preparing transaction: done
Verifying transaction: done
Executing transaction: done
#
# To activate this environment, use
#
#     $ conda activate streamlit-env
#
# To deactivate an active environment, use
#
#     $ conda deactivate
```

激活环境，并安装streamlit：

``` powershell
❯ conda activate streamlit-env

(streamlit-env)
~ via 🐍 v3.9.16 via 🅒 streamlit-env
❯ pip install streamlit
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Collecting streamlit
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/75/83/aab60f6a6d9317ff546b67803d17027c381aedfbb29b45507784ad77f889/streamlit-1.18.1-py2.py3-none-any.whl (9.6 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 9.6/9.6 MB 357.6 kB/s eta 0:00:00
Collecting numpy
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/9d/3b/13404993b5dec7403abcf9518569316b5d72d9a3081cd90aca130e6d8b00/numpy-1.24.2-cp39-cp39-win_amd64.whl (14.9 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 14.9/14.9 MB 385.5 kB/s eta 0:00:00
Collecting pandas>=0.25
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/c2/45/801ecd8434eef0b39cc02795ffae273fe3df3cfcb3f6fff215efbe92d93c/pandas-1.5.3-cp39-cp39-win_amd64.whl (10.9 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 10.9/10.9 MB 1.2 MB/s eta 0:00:00
Collecting pympler>=0.9
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/2c/42/41e1469ed0b37b9c8532cb8074bea179f7d85ee7e82a59b5b6c289ed6045/Pympler-1.0.1-py3-none-any.whl (164 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 164.8/164.8 kB 2.5 MB/s eta 0:00:00
Collecting pillow>=6.2.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/fd/41/6e44769918a4a2f5294a19bbbf12f58138fcb0c1c3df4721bc5fe1c6f3bf/Pillow-9.4.0-cp39-cp39-win_amd64.whl (2.5 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2.5/2.5 MB 2.2 MB/s eta 0:00:00
Collecting blinker>=1.0.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/30/41/caa5da2dbe6d26029dfe11d31dfa8132b4d6d30b6d6b61a24824075a5f06/blinker-1.5-py2.py3-none-any.whl (12 kB)
Collecting pydeck>=0.1.dev5
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/ac/1e/676ed6c028bfd39d6c7c75abd57bc482f7eaa813f3faa715d80431732a43/pydeck-0.8.0-py2.py3-none-any.whl (4.7 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 4.7/4.7 MB 1.1 MB/s eta 0:00:00
Collecting altair>=3.2.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/18/62/47452306e84d4d2e67f9c559380aeb230f5e6ca84fafb428dd36b96a99ba/altair-4.2.2-py3-none-any.whl (813 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 813.6/813.6 kB 349.8 kB/s eta 0:00:00
Collecting tornado>=6.0.3
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/1c/26/cbfa1103e74a02e09dd53291e419da3ad4c5b948f52aea5800e6671b56e0/tornado-6.2-cp37-abi3-win_amd64.whl (425 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 425.3/425.3 kB 1.9 MB/s eta 0:00:00
Collecting watchdog
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/6d/7f/8deebfbec704ab40e2fe9064c1b157a9a7cd7061f64f7164fe5f902ce0fc/watchdog-2.2.1-py3-none-win_amd64.whl (78 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 79.0/79.0 kB 1.5 MB/s eta 0:00:00
Collecting toml
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/44/6f/7120676b6d73228c96e17f1f794d8ab046fc910d781c8d151120c3f1569e/toml-0.10.2-py2.py3-none-any.whl (16 kB)
Collecting protobuf<4,>=3.12
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/11/a5/e52b731415ad6ef3d841e9e6e337a690249e800cc7c06f0749afab26348c/protobuf-3.20.3-cp39-cp39-win_amd64.whl (904 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 904.2/904.2 kB 1.8 MB/s eta 0:00:00
Collecting click>=7.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/c2/f1/df59e28c642d583f7dacffb1e0965d0e00b218e0186d7858ac5233dce840/click-8.1.3-py3-none-any.whl (96 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 96.6/96.6 kB 918.6 kB/s eta 0:00:00
Collecting tzlocal>=1.1
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/31/b7/3bc2c1868f27677139b772e4fde95265b93151912fd90eb874827943bfcf/tzlocal-4.2-py3-none-any.whl (19 kB)
Collecting packaging>=14.1
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/ed/35/a31aed2993e398f6b09a790a181a7927eb14610ee8bbf02dc14d31677f1c/packaging-23.0-py3-none-any.whl (42 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 42.7/42.7 kB 2.0 MB/s eta 0:00:00
Collecting requests>=2.4
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/d2/f4/274d1dbe96b41cf4e0efb70cbced278ffd61b5c7bb70338b62af94ccb25b/requests-2.28.2-py3-none-any.whl (62 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 62.8/62.8 kB 1.1 MB/s eta 0:00:00
Collecting cachetools>=4.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/db/14/2b48a834d349eee94677e8702ea2ef98b7c674b090153ea8d3f6a788584e/cachetools-5.3.0-py3-none-any.whl (9.3 kB)
Collecting pyarrow>=4.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/5c/a0/9ec61020d9bf28cb965f214040770a5c5dbefd009aaeeb1a0cb0d50a66c4/pyarrow-11.0.0-cp39-cp39-win_amd64.whl (20.6 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 20.6/20.6 MB 1.6 MB/s eta 0:00:00
Collecting python-dateutil
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/36/7a/87837f39d0296e723bb9b62bbb257d0355c7f6128853c78955f57342a56d/python_dateutil-2.8.2-py2.py3-none-any.whl (247 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 247.7/247.7 kB 1.9 MB/s eta 0:00:00
Collecting typing-extensions>=3.10.0.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/31/25/5abcd82372d3d4a3932e1fa8c3dbf9efac10cc7c0d16e78467460571b404/typing_extensions-4.5.0-py3-none-any.whl (27 kB)
Collecting rich>=10.11.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/a8/c6/14b77fe7a5fab66ffbeffd6706f598d00a52702846bce0e2339bcf9dd20c/rich-13.3.1-py3-none-any.whl (239 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 239.0/239.0 kB 1.2 MB/s eta 0:00:00
Collecting semver
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/0b/70/b84f9944a03964a88031ef6ac219b6c91e8ba2f373362329d8770ef36f02/semver-2.13.0-py2.py3-none-any.whl (12 kB)
Collecting gitpython!=3.1.19
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/9e/8a/d1e02cc111d65b0346f70abb83c51f8593e7134bf694a4a56d1a470caaf7/GitPython-3.1.31-py3-none-any.whl (184 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 184.3/184.3 kB 1.6 MB/s eta 0:00:00
Collecting importlib-metadata>=1.4
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/26/a7/9da7d5b23fc98ab3d424ac2c65613d63c1f401efb84ad50f2fa27b2caab4/importlib_metadata-6.0.0-py3-none-any.whl (21 kB)
Collecting validators>=0.2
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/95/14/ed0af6865d378cfc3c504aed0d278a890cbefb2f1934bf2dbe92ecf9d6b1/validators-0.20.0.tar.gz (30 kB)
  Preparing metadata (setup.py) ... done
Collecting entrypoints
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/35/a8/365059bbcd4572cbc41de17fd5b682be5868b218c3c5479071865cab9078/entrypoints-0.4-py3-none-any.whl (5.3 kB)
Collecting jsonschema>=3.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/c1/97/c698bd9350f307daad79dd740806e1a59becd693bd11443a0f531e3229b3/jsonschema-4.17.3-py3-none-any.whl (90 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 90.4/90.4 kB 2.6 MB/s eta 0:00:00
Collecting toolz
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/7f/5c/922a3508f5bda2892be3df86c74f9cf1e01217c2b1f8a0ac4841d903e3e9/toolz-0.12.0-py3-none-any.whl (55 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 55.8/55.8 kB 1.5 MB/s eta 0:00:00
Collecting jinja2
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/bc/c3/f068337a370801f372f2f8f6bad74a5c140f6fda3d9de154052708dd3c65/Jinja2-3.1.2-py3-none-any.whl (133 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 133.1/133.1 kB 2.0 MB/s eta 0:00:00
Collecting colorama
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/d1/d6/3965ed04c63042e047cb6a3e6ed1a63a35087b6a609aa3a15ed8ac56c221/colorama-0.4.6-py2.py3-none-any.whl (25 kB)
Collecting gitdb<5,>=4.0.1
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/21/a6/35f83efec687615c711fe0a09b67e58f6d1254db27b1013119de46f450bd/gitdb-4.0.10-py3-none-any.whl (62 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 62.7/62.7 kB 3.5 MB/s eta 0:00:00
Collecting zipp>=0.5
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/a8/7d/90189265f0a9bcdf79b1143b77b0ef6dca0a5f13783f1e1efa4d7d7eafca/zipp-3.14.0-py3-none-any.whl (6.7 kB)
Collecting pytz>=2020.1
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/2e/09/fbd3c46dce130958ee8e0090f910f1fe39e502cc5ba0aadca1e8a2b932e5/pytz-2022.7.1-py2.py3-none-any.whl (499 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 499.4/499.4 kB 1.6 MB/s eta 0:00:00
Collecting six>=1.5
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/d9/5a/e7c31adbe875f2abbb91bd84cf2dc52d792b5a01506781dbcf25c91daf11/six-1.16.0-py2.py3-none-any.whl (11 kB)
Collecting urllib3<1.27,>=1.21.1
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/fe/ca/466766e20b767ddb9b951202542310cba37ea5f2d792dae7589f1741af58/urllib3-1.26.14-py2.py3-none-any.whl (140 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 140.6/140.6 kB 490.2 kB/s eta 0:00:00
Collecting charset-normalizer<4,>=2
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/e7/0d/5eaceb5abfc000cca204af9f50e9839462dc0bb1c4e0f4b14ed370e3febd/charset_normalizer-3.0.1-cp39-cp39-win_amd64.whl (96 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 96.5/96.5 kB 2.8 MB/s eta 0:00:00
Collecting idna<4,>=2.5
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/fc/34/3030de6f1370931b9dbb4dad48f6ab1015ab1d32447850b9fc94e60097be/idna-3.4-py3-none-any.whl (61 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 61.5/61.5 kB 234.2 kB/s eta 0:00:00
Requirement already satisfied: certifi>=2017.4.17 in c:\users\yp.wang\anaconda3\envs\streamlit-env\lib\site-packages (from requests>=2.4->streamlit) (2022.12.7)
Collecting pygments<3.0.0,>=2.14.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/0b/42/d9d95cc461f098f204cd20c85642ae40fbff81f74c300341b8d0e0df14e0/Pygments-2.14.0-py3-none-any.whl (1.1 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 1.1/1.1 MB 1.1 MB/s eta 0:00:00
Collecting markdown-it-py<3.0.0,>=2.1.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/bf/25/2d88e8feee8e055d015343f9b86e370a1ccbec546f2865c98397aaef24af/markdown_it_py-2.2.0-py3-none-any.whl (84 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 84.5/84.5 kB 1.2 MB/s eta 0:00:00
Collecting pytz-deprecation-shim
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/eb/73/3eaab547ca809754e67e06871cff0fc962bafd4b604e15f31896a0f94431/pytz_deprecation_shim-0.1.0.post0-py2.py3-none-any.whl (15 kB)
Collecting tzdata
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/fa/5e/f99a7df3ae2079211d31ec23b1d34380c7870c26e99159f6e422dcbab538/tzdata-2022.7-py2.py3-none-any.whl (340 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 340.1/340.1 kB 439.9 kB/s eta 0:00:00
Collecting decorator>=3.4.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/d5/50/83c593b07763e1161326b3b8c6686f0f4b0f24d5526546bee538c89837d6/decorator-5.1.1-py3-none-any.whl (9.1 kB)
Collecting smmap<6,>=3.0.1
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/6d/01/7caa71608bc29952ae09b0be63a539e50d2484bc37747797a66a60679856/smmap-5.0.0-py3-none-any.whl (24 kB)
Collecting MarkupSafe>=2.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/46/0c/10ee33673c5e36fa3809cf136971f81d951ca38516188ee11a965d9b2fe9/MarkupSafe-2.1.2-cp39-cp39-win_amd64.whl (16 kB)
Collecting pyrsistent!=0.17.0,!=0.17.1,!=0.17.2,>=0.14.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/c3/c7/185e37df78c1e34c14961cbd7c89945e27825b5a41bf455e2df2dd46e18e/pyrsistent-0.19.3-cp39-cp39-win_amd64.whl (62 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 62.7/62.7 kB 1.7 MB/s eta 0:00:00
Collecting attrs>=17.4.0
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/fb/6e/6f83bf616d2becdf333a1640f1d463fef3150e2e926b7010cb0f81c95e88/attrs-22.2.0-py3-none-any.whl (60 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 60.0/60.0 kB 1.1 MB/s eta 0:00:00
Collecting mdurl~=0.1
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/b3/38/89ba8ad64ae25be8de66a6d463314cf1eb366222074cfda9ee839c56a4b4/mdurl-0.1.2-py3-none-any.whl (10.0 kB)
Building wheels for collected packages: validators
  Building wheel for validators (setup.py) ... done
  Created wheel for validators: filename=validators-0.20.0-py3-none-any.whl size=19591 sha256=6be2d241d0ee322ffc02b86a4fba6d6417764a38a60a55307246ac4c0b5db3a5
  Stored in directory: c:\users\yp.wang\appdata\local\pip\cache\wheels\93\fb\28\2cfa3bfb17c8e3bd3c1b22853cbfe4cabd25dbb42bb4afd517
Successfully built validators
Installing collected packages: pytz, charset-normalizer, zipp, watchdog, urllib3, tzdata, typing-extensions, tornado, toolz, toml, smmap, six, semver, pyrsistent, pympler, pygments, protobuf, pillow, packaging, numpy, mdurl, MarkupSafe, idna, entrypoints, decorator, colorama, cachetools, blinker, attrs, validators, requests, pytz-deprecation-shim, python-dateutil, pyarrow, markdown-it-py, jsonschema, jinja2, importlib-metadata, gitdb, click, tzlocal, rich, pydeck, pandas, gitpython, altair, streamlit
Successfully installed MarkupSafe-2.1.2 altair-4.2.2 attrs-22.2.0 blinker-1.5 cachetools-5.3.0 charset-normalizer-3.0.1 click-8.1.3 colorama-0.4.6 decorator-5.1.1 entrypoints-0.4 gitdb-4.0.10 gitpython-3.1.31 idna-3.4 importlib-metadata-6.0.0 jinja2-3.1.2 jsonschema-4.17.3 markdown-it-py-2.2.0 mdurl-0.1.2 numpy-1.24.2 packaging-23.0 pandas-1.5.3 pillow-9.4.0 protobuf-3.20.3 pyarrow-11.0.0 pydeck-0.8.0 pygments-2.14.0 pympler-1.0.1 pyrsistent-0.19.3 python-dateutil-2.8.2 pytz-2022.7.1 pytz-deprecation-shim-0.1.0.post0 requests-2.28.2 rich-13.3.1 semver-2.13.0 six-1.16.0 smmap-5.0.0 streamlit-1.18.1 toml-0.10.2 toolz-0.12.0 tornado-6.2 typing-extensions-4.5.0 tzdata-2022.7 tzlocal-4.2 urllib3-1.26.14 validators-0.20.0 watchdog-2.2.1 zipp-3.14.0
```

streamlit的依赖项还是挺多的，这个环境几乎涵盖了用python做数据科学所需的包，所以除了学习streamlit外，其它跟数据科学相关的项目也可以用这个环境。

接下来，一行代码把streamlit运行起来：

``` powershell
(streamlit-env)
~ via 🐍 v3.9.16 via 🅒 streamlit-env
❯ streamlit hello

  Welcome to Streamlit. Check out our demo in your browser.

  Local URL: http://localhost:8501
  Network URL: http://192.168.20.81:8501

  Ready to create your own Python apps super quickly?
  Head over to https://docs.streamlit.io

  May you create awesome apps!
```

这样就跑起来文章开头那个demo了。

可以使用 <kbd> ctrl </kbd> + <kbd> c </kbd> 停止运行程序，然后 `conda deactivate` 退出虚拟环境。
