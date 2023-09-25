---
title: python项目打包上传pypi
date: 2023-09-25 10:23:13
categories:
tags:
    - python
---

## PyPI

PyPI（Python Package Index）是Python编程语言中最大的第三方软件包仓库，它是Python生态系统的核心组成部分。PyPI提供了一个集中存储和分发Python软件包的平台，为开发者和用户提供了便利的方式来查找、安装和分享各种Python软件包。

在PyPI上，开发者可以发布自己的Python软件包，而其他开发者和用户则可以轻松地通过pip等工具安装这些软件包。PyPI中的软件包涵盖了各种领域，包括Web开发、数据科学、机器学习、人工智能等。无论是初学者还是有经验的开发者，都能从PyPI中找到适合自己需求的软件包。

使用PyPI非常简单，只需在命令行中运行`pip install package-name`命令，就能安装你需要的软件包。同时，PyPI支持版本控制，使得开发者可以灵活地选择软件包的特定版本进行安装。这为项目开发和维护提供了方便。

<!-- more -->

作为一个开放的平台，PyPI鼓励开发者共享他们的项目和代码，并与其他开发者合作。通过PyPI，开发者可以轻松获取其他人的优秀代码，并在自己的项目中重用。这种共享和合作精神促进了Python社区的繁荣和创新。

PyPI在Python生态系统中扮演着关键的角色，它提供了一个开放、共享和可访问的平台，使得Python开发者能够更加高效地开发项目、分享代码、并与全球开发者社区共同成长。无论是新手还是专业开发者，都应该充分利用PyPI的优势，探索更多有趣和有用的Python软件包，为自己的项目增添活力与创新。

## 示例项目

首先，更新`pip`工具，然后创建一个项目目录：

```powershell
pip install --upgrade pip
mkdir packaging_example && cd packaging_example
```

接着创建如下目录结构：

```powershell
packaging_example/
├── LICENSE
├── pyproject.toml
├── README.md
├── src/
│   └── example_package_YOUR_USERNAME_HERE/
│       ├── __init__.py
│       └── example.py
└── tests/
```

`YOUR_USERNAME_HERE`要取一个独特点的名字，以避免跟其他人上传的包冲突，我这里起的名字是`peter777`😀

包含Python文件的目录应与项目名称匹配。这样可以简化配置，并对安装该包的用户更加明显。

`__init__.py`文件是必需的，以便将该目录作为一个包进行导入，并且应该是空的。

`example.py`是包中一个模块的示例，其中可以包含你的包的逻辑（函数、类、常量等）。打开该文件并输入以下内容：

```python
def add_one(number):
    return number + 1
```

so easy! 如果你对Python的模块和导入包不熟悉，请花几分钟阅读[Python的包和模块文档](https://docs.python.org/3/tutorial/modules.html#packages)。

### `pyproject.toml`

`pyproject.toml`文件告诉"frontend"构建工具（如pip和build工具）要使用什么"backend"工具来为你的项目创建分发包。你可以从多个后端工具中选择。本文使用setuptools，但与Hatchling、Flit、PDM等支持metadata项目表的其他工具的工作方式相同。

> 注意：
> 
> 某些构建后端是更大工具的一部分，它们提供命令行界面和额外功能，如项目初始化和版本管理，以及构建、上传和安装软件包。本文使用单一目的的独立工具。

打开`pyproject.toml`并输入：

```toml
[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"
```

`requires` 是一个包含构建你的软件包所需依赖的列表。你不需要手动安装这些依赖，构建工具（如pip）会在构建过程中自动在一个临时的、隔离的虚拟环境中安装它们。

`build-backend` 是构建工具在执行构建过程时所使用的 Python 对象的名称。它指定了用于构建你的项目的后端工具。不同的构建工具可能使用不同的后端工具来执行构建操作。在 `pyproject.toml` 文件中，你可以指定你想要使用的后端工具名称。例如，如果你想使用 `setuptools` 来构建你的项目，可以将 `build-backend` 设置为 `"setuptools.build_meta"`。这样一来，构建工具就会使用 `setuptools` 来执行项目的构建过程。

接下来在`pyproject.toml`中继续填写项目元数据:

```toml
[project]
name = "example_package_YOUR_USERNAME_HERE"
version = "0.0.1"
authors = [
  { name="Example Author", email="author@example.com" },
]
description = "A small example package"
readme = "README.md"
requires-python = ">=3.7"
classifiers = [
    "Programming Language :: Python :: 3",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
]

[project.urls]
"Homepage" = "https://github.com/pypa/sampleproject"
"Bug Tracker" = "https://github.com/pypa/sampleproject/issues"
```

将 `YOUR_USERNAME_HERE` 替换为你的用户名，确保你拥有一个独特的软件包名称，避免与其他遵循本教程的人上传的软件包冲突。

- `name` 是你的软件包的分发名称。它可以是任何名称，只要包含字母、数字、下划线、连字符和点号。同时，它在 PyPI 上不能与已有的软件包名称重复。请确保在本教程中使用你的用户名作为名称，以确保你不会尝试上传与现有包同名的软件包。

- `version` 是软件包的版本号。可以参考版本说明规范了解更多关于版本号的细节。某些构建后端可能允许通过其他方式指定版本号，例如从文件或git标签中获取。

- `authors` 用于标识软件包的作者。你需要为每个作者指定姓名和电子邮件。也可以以相同格式列出维护者（maintainers）。

- `description` 是对软件包的简短、一句话概述。

- `readme` 是一个指向包含软件包详细描述的文件的路径。这将在 PyPI 上的软件包详情页面上显示。在本例中，描述从 `README.md` 文件中加载（这是一种常见的模式）。还有一种更高级的表格形式，详细描述在项目元数据规范中。

- `requires-python` 指定你的项目支持的Python版本。安装工具如pip会回溯查找旧版本的软件包，直到找到一个与匹配的Python版本相符的版本。

- `classifiers` 给索引和pip一些关于你的软件包的额外元数据。在本例中，软件包仅与Python 3兼容，采用MIT许可证，且不依赖于特定操作系统。你应该至少包含你的软件包适用的Python版本、软件包所使用的许可证以及支持的操作系统信息。有关分类器的完整列表，请参阅 https://pypi.org/classifiers/。

- `urls` 允许你列出任意数量的额外链接，以在PyPI上展示。通常这可以用来提供源代码、文档、问题跟踪器等链接。

可以参阅[项目元数据规范（Project Metadata Specification）](https://packaging.python.org/en/latest/specifications/declaring-project-metadata/#declaring-project-metadata)了解更多字段细节和定义内容。

接下来就可以开始打包了，打包前需要先安装`build`包，然后打包：

```powershell
❯ python -m pip install --upgrade build
❯ python -m build
* Creating virtualenv isolated environment...
* Installing packages in isolated environment... (setuptools>=61.0)
* Getting build dependencies for sdist...
running egg_info
creating src\example_package_peter777.egg-info
writing src\example_package_peter777.egg-info\PKG-INFO
writing dependency_links to src\example_package_peter777.egg-info\dependency_links.txt
writing top-level names to src\example_package_peter777.egg-info\top_level.txt
writing manifest file 'src\example_package_peter777.egg-info\SOURCES.txt'
reading manifest file 'src\example_package_peter777.egg-info\SOURCES.txt'
adding license file 'LICENSE'
writing manifest file 'src\example_package_peter777.egg-info\SOURCES.txt'
* Building sdist...
running sdist
running egg_info
writing src\example_package_peter777.egg-info\PKG-INFO
writing dependency_links to src\example_package_peter777.egg-info\dependency_links.txt
writing top-level names to src\example_package_peter777.egg-info\top_level.txt
reading manifest file 'src\example_package_peter777.egg-info\SOURCES.txt'
adding license file 'LICENSE'
writing manifest file 'src\example_package_peter777.egg-info\SOURCES.txt'
running check
creating example_package_peter777-0.0.1
creating example_package_peter777-0.0.1\src
creating example_package_peter777-0.0.1\src\example_package_peter777
creating example_package_peter777-0.0.1\src\example_package_peter777.egg-info
copying files to example_package_peter777-0.0.1...
copying LICENSE -> example_package_peter777-0.0.1
copying README.md -> example_package_peter777-0.0.1
copying pyproject.toml -> example_package_peter777-0.0.1
copying src\example_package_peter777\__init__.py -> example_package_peter777-0.0.1\src\example_package_peter777
copying src\example_package_peter777\example.py -> example_package_peter777-0.0.1\src\example_package_peter777
copying src\example_package_peter777.egg-info\PKG-INFO -> example_package_peter777-0.0.1\src\example_package_peter777.egg-info
copying src\example_package_peter777.egg-info\SOURCES.txt -> example_package_peter777-0.0.1\src\example_package_peter777.egg-info
copying src\example_package_peter777.egg-info\dependency_links.txt -> example_package_peter777-0.0.1\src\example_package_peter777.egg-info
copying src\example_package_peter777.egg-info\top_level.txt -> example_package_peter777-0.0.1\src\example_package_peter777.egg-info
Writing example_package_peter777-0.0.1\setup.cfg
Creating tar archive
removing 'example_package_peter777-0.0.1' (and everything under it)
* Building wheel from sdist
* Creating virtualenv isolated environment...
* Installing packages in isolated environment... (setuptools>=61.0)
* Getting build dependencies for wheel...
running egg_info
writing src\example_package_peter777.egg-info\PKG-INFO
writing dependency_links to src\example_package_peter777.egg-info\dependency_links.txt
writing top-level names to src\example_package_peter777.egg-info\top_level.txt
reading manifest file 'src\example_package_peter777.egg-info\SOURCES.txt'
adding license file 'LICENSE'
writing manifest file 'src\example_package_peter777.egg-info\SOURCES.txt'
* Installing packages in isolated environment... (wheel)
* Building wheel...
running bdist_wheel
running build
running build_py
creating build
creating build\lib
creating build\lib\example_package_peter777
copying src\example_package_peter777\example.py -> build\lib\example_package_peter777
copying src\example_package_peter777\__init__.py -> build\lib\example_package_peter777
running egg_info
writing src\example_package_peter777.egg-info\PKG-INFO
writing dependency_links to src\example_package_peter777.egg-info\dependency_links.txt
writing top-level names to src\example_package_peter777.egg-info\top_level.txt
reading manifest file 'src\example_package_peter777.egg-info\SOURCES.txt'
adding license file 'LICENSE'
writing manifest file 'src\example_package_peter777.egg-info\SOURCES.txt'
installing to build\bdist.win-amd64\wheel
running install
running install_lib
creating build\bdist.win-amd64
creating build\bdist.win-amd64\wheel
creating build\bdist.win-amd64\wheel\example_package_peter777
copying build\lib\example_package_peter777\example.py -> build\bdist.win-amd64\wheel\.\example_package_peter777
copying build\lib\example_package_peter777\__init__.py -> build\bdist.win-amd64\wheel\.\example_package_peter777
running install_egg_info
Copying src\example_package_peter777.egg-info to build\bdist.win-amd64\wheel\.\example_package_peter777-0.0.1-py3.8.egg-info
running install_scripts
creating build\bdist.win-amd64\wheel\example_package_peter777-0.0.1.dist-info\WHEEL
creating 'C:\Users\yp.wang\OneDrive - hi2me\working\packaging_example\dist\.tmp-yy65h9xp\example_package_peter777-0.0.1-py3-none-any.whl' and adding 'build\bdist.win-amd64\wheel' to it
adding 'example_package_peter777/__init__.py'
adding 'example_package_peter777/example.py'
adding 'example_package_peter777-0.0.1.dist-info/LICENSE'
adding 'example_package_peter777-0.0.1.dist-info/METADATA'
adding 'example_package_peter777-0.0.1.dist-info/WHEEL'
adding 'example_package_peter777-0.0.1.dist-info/top_level.txt'
adding 'example_package_peter777-0.0.1.dist-info/RECORD'
removing build\bdist.win-amd64\wheel
Successfully built example_package_peter777-0.0.1.tar.gz and example_package_peter777-0.0.1-py3-none-any.whl
```

看到最后一行就说明打包成功了。是时候将软件包上传到Python Package Index了！

首先，需要在TestPyPI上注册一个帐户。TestPyPI是包索引的一个单独实例，旨在用于测试和实验。对于像本文这样的实验，我们不希望上传到真正的索引，因此TestPyPI非常适合。要注册一个帐户，请访问 https://test.pypi.org/account/register/ 并按照页面上的步骤完成注册。在上传任何软件包之前，还需要验证电子邮件地址。更多细节请参阅[《使用TestPyPI》](https://packaging.python.org/en/latest/guides/using-testpypi/)。

为了安全地上传你的项目，你需要一个PyPI API令牌。在 https://test.pypi.org/manage/account/#api-tokens 上创建一个令牌，将"Scope"设置为"Entire account"。在复制和保存令牌之前，不要关闭该页面，因为你之后将无法再看到这个令牌。

现在，我已经注册好了，可以使用Twine工具上传分发包。需要安装Twine：

```powershell
python -m pip install --upgrade twine
```

安装完成后，就可以使用Twine上传软件包到TestPyPI。上传的命令如下：

```powershell
❯ python -m twine upload --repository testpypi dist/*
Uploading distributions to https://test.pypi.org/legacy/
Enter your username: __token__
Enter your password:
Uploading example_package_peter777-0.0.1-py3-none-any.whl
100% ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 17.6/17.6 kB • 00:00 • ?
Uploading example_package_peter777-0.0.1.tar.gz
100% ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 17.2/17.2 kB • 00:00 • ?

View at:
https://test.pypi.org/project/example-package-peter777/0.0.1/
```

上传过程会收到一个用户名和密码的提示。对于用户名，请使用`__token__`。对于密码，请使用令牌值，包括前缀`pypi-`。密码粘贴时不会有任何字符提示，确定正确继续执行就可以。

这样就上传成功了，接下来用`pip`安装验证下：

```powershell
❯ pip install -i https://test.pypi.org/simple/ example-package-peter777==0.0.1
Defaulting to user installation because normal site-packages is not writeable
Looking in indexes: https://test.pypi.org/simple/
Collecting example-package-peter777==0.0.1
  Obtaining dependency information for example-package-peter777==0.0.1 from https://test-files.pythonhosted.org/packages/fe/2e/f7b20dd99cbf107e44382654fd410cb5edce9162412e23710bce1399bcf7/example_package_peter777-0.0.1-py3-none-any.whl.metadata
  Downloading https://test-files.pythonhosted.org/packages/fe/2e/f7b20dd99cbf107e44382654fd410cb5edce9162412e23710bce1399bcf7/example_package_peter777-0.0.1-py3-none-any.whl.metadata (573 bytes)
Downloading https://test-files.pythonhosted.org/packages/fe/2e/f7b20dd99cbf107e44382654fd410cb5edce9162412e23710bce1399bcf7/example_package_peter777-0.0.1-py3-none-any.whl (14 kB)
Installing collected packages: example-package-peter777
Successfully installed example-package-peter777-0.0.1

~ via 🐍 v3.8.10 took 4s
❯ python
Python 3.8.10 (tags/v3.8.10:3d8993a, May  3 2021, 11:48:03) [MSC v.1928 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> from example-package-peter777 import example
  File "<stdin>", line 1
    from example-package-peter777 import example
                ^
SyntaxError: invalid syntax
>>> from example_package_peter777 import example
>>> example.add_one(5)
6
```

注意，虽然上传到pypi的路径是`example-package-peter777`，`pip`安装时也是`pip install -i https://test.pypi.org/simple/ example-package-peter777==0.0.1`，但是在导入包时应使用`example_package_peter777`。
