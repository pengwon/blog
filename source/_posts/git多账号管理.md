---
title: git多账号管理
date: 2023-04-21 16:07:21
categories:
tags:
  - git
---

# Git 多账号管理的最佳实践

在一些场景下，我们需要使用 Git 进行多个账号管理，例如我们可能会有一个公司的 Git 账号和一个私人 Git 账号。这时候，我们需要采用一些技巧来使得我们可以在同一台电脑上使用多个 Git 账号。

最佳原则是：

1. 尽量只使用一个统一的账号。
2. 如果不得不使用多个账号，则最好为每个仓库单独设置用户名和邮箱。

<!-- more -->

## 为每个账号设置不同的用户名和邮箱

Git 使用用户名和邮箱来记录每次提交的作者信息，因此我们可以通过为每个账号设置不同的用户名和邮箱来实现多账号管理。具体步骤如下：

1. 打开终端或 Git Bash，进入项目的根目录；
2. 输入以下命令，为当前项目设置用户名和邮箱：

```
git config user.name "your_username"
git config user.email "your_email@example.com"
```

3. 如果你需要为多个项目设置不同的用户名和邮箱，则可以使用 `--global` 参数来全局设置。这样，所有项目都将使用相同的用户名和邮箱：

```
git config --global user.name "your_username"
git config --global user.email "your_email@example.com"
```

## 使用 SSH 钥匙对进行认证

使用 SSH 钥匙对是一种比使用用户名和密码更安全的认证方式。为每个 Git 账号创建不同的 SSH 钥匙对，然后将其添加到相应的 Git 账号中，即可实现多账号管理。具体步骤如下：

1. 为每个 Git 账号创建不同的 SSH 钥匙对。可以使用以下命令生成 SSH 钥匙对：

```
ssh-keygen -t rsa -C "your_email@example.com"
```

2. 在 Git 服务提供商的网站中添加 SSH 公钥。在 GitHub 中，可以在 Settings > SSH and GPG keys 中添加 SSH 公钥；
3. 使用 SSH 地址来克隆项目，例如：

```
git clone git@github.com:your_username/your_repository.git
```

## 使用 Git 辅助工具

为了更加方便地管理多个 Git 账号，我们可以使用一些 Git 辅助工具。以下是一些常用的工具：

- **git-credential-cache：** 将 Git 凭证存储在缓存中，避免每次输入账号密码；
- **git-credential-store：** 将 Git 凭证保存在明文文件中；
- **git-credential-osxkeychain：** 将 Git 凭证保存在 Mac OS X 的钥匙串中；
- **git-credential-winstore：** 将 Git 凭证保存在 Windows 的凭证管理器中。

使用这些工具可以方便地管理多个 Git 账号，避免频繁输入账号密码。具体使用方法可以参考相应的文档。

对于https方式，现在新版本的git已经可以使用新的[GCM(Git Credential Manager)](https://github.com/git-ecosystem/git-credential-manager)，支持更友好的账号密码管理方式。

## 总结

通过上述方法，我们可以轻松地实现 Git 多账号管理。需要注意的是，在使用 SSH 钥匙对进行认证时，需要为每个账号单独设置密钥。
