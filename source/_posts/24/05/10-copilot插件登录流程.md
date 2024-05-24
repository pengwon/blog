---
title: GitHub Copilot插件登录流程
date: 2024-05-10 11:24:53
categories:
tags:
    - github
    - ai
---


GitHub OAuth应用有一套流程，用于获取用户的授权信息。这个流程是一个OAuth2的流程，主要分为以下几个步骤：

1. 用户点击登录按钮，触发登录请求。
2. 应用将用户重定向到GitHub的授权页面。
3. 用户授权应用访问其GitHub账号。
4. GitHub验证用户的授权，并生成一个授权码。
5. 应用使用授权码向GitHub请求访问令牌。
6. GitHub验证授权码，并返回访问令牌。
7. 应用使用访问令牌向GitHub请求用户信息。
8. GitHub返回用户信息。

这个流程是一个标准的OAuth2流程，用于获取用户的授权信息。应用可以使用这个流程获取用户的授权信息，以便进行后续的操作。

而Copilot插件作为GitHub的产品，许可授权必然使用GitHub的验证流程。但是，Copilot插件又支持多种编辑器和IDE，还要受到各种限制，所以登录流程可能会有所不同。

<!-- more -->

在VSCode中，Copilot插件优先使用浏览器验证用户登录，必须要在本地操作才行。

GitHub OAuth应用有一种设备登录流程，设备流允许授权用户使用无头应用程序，例如 CLI 工具或 Git 凭据管理器。

在使用设备流识别和授权用户之前，必须先在应用的设置中启用它。

## 设备流程概述

1. 您的应用程序会请求设备和用户验证码，并获取用户将在其中输入用户验证码的授权 URL。
2. 应用程序提示用户在 https://github.com/login/device 中输入用户验证码。
3. 应用程序轮询用户身份验证状态。 用户授权设备后，应用程序将能够使用新的访问令牌进行 API 调用。

### 第 1 步：应用程序从 GitHub 请求设备和用户验证码

```
POST https://github.com/login/device/code
```

应用程序必须请求用户验证码和验证 URL，因为应用程序在下一步中提示用户进行身份验证时将使用它们。 此请求还返回设备验证代码，应用程序必须使用它们来接收访问令牌和检查用户身份验证的状态。

终结点采用以下输入参数。

| 参数名称 | 类型   | 说明                                       |
| -------- | ------ | ------------------------------------------ |
| client_id| string | 必填。 从 GitHub 收到的应用的客户端 ID。    |
| scope    | string | 应用请求访问的范围的列表（以空格分隔）。   |

默认情况下，响应采用以下形式：

```
device_code=3584d83530557fdd1f46af8289938c8ef79f9dc5&expires_in=900&interval=5&user_code=WDJB-MJHT&verification_uri=https%3A%2F%2Fgithub.com%2Flogin%2Fdevice
```

| 参数名称         | 类型    | 说明 |
| --------------- | ------- | ---- |
| device_code     | string  | 设备验证码为 40 个字符，用于验证设备。 |
| user_code       | string  | 用户验证码显示在设备上，以便用户可以在浏览器中输入该代码。 此代码为 8 个字符，中间有连字符。 |
| verification_uri| string  | 用户需要在其中输入 user_code 的验证 URL： https://github.com/login/device。 |
| expires_in      | integer | device_code 和 user_code 过期之前的秒数。 默认值为 900 秒或 15 分钟。 |
| interval        | integer | 在能够发出新的访问令牌请求 (POST https://github.com/login/oauth/access_token) 以完成设备授权之前必须经过的最短秒数。 例如，如果间隔为 5，则只有经过 5 秒后才能发出新请求。 如果在 5 秒内发出多个请求，则将达到速率限制并收到 slow_down 错误。 |

如果在 Accept 标头中提供格式，则还可以接收不同格式的响应。 例如 Accept: application/json 或 Accept: application/xml：

```
Accept: application/json
{
  "device_code": "3584d83530557fdd1f46af8289938c8ef79f9dc5",
  "user_code": "WDJB-MJHT",
  "verification_uri": "https://github.com/login/device",
  "expires_in": 900,
  "interval": 5
}
```

```
Accept: application/xml
<OAuth>
  <device_code>3584d83530557fdd1f46af8289938c8ef79f9dc5</device_code>
  <user_code>WDJB-MJHT</user_code>
  <verification_uri>https://github.com/login/device</verification_uri>
  <expires_in>900</expires_in>
  <interval>5</interval>
</OAuth>
```

### 第 2 步：提示用户在浏览器中输入用户代码

设备将显示用户验证码并提示用户在 https://github.com/login/device 中输入该代码。

### 第 3 步：应用程序轮询 GitHub 以检查用户是否授权设备

```
POST https://github.com/login/oauth/access_token
```

应用将发出轮询 `POST https://github.com/login/oauth/access_token` 的设备授权请求，直到设备和用户代码过期，或者用户已使用有效的用户代码成功授权应用。 应用必须使用在步骤 1 中检索到的最短轮询 interval，以免出现速率限制错误。 

用户必须在 15 分钟（或 900 秒内）内输入有效代码。 15 分钟后，需要使用 `POST https://github.com/login/device/code` 请求新的设备授权代码。

一旦用户授权， 应用程序将收到一个访问令牌，该令牌可用于代表用户向 API 发出请求。

终结点采用以下输入参数。

| 参数名称     | 类型   | 说明 |
| ------------ | ------ | ---- |
| client_id    | string | 必填。 从 GitHub 收到的 OAuth app 的客户端 ID。 |
| device_code  | string | 必需。 你从 POST https://github.com/login/device/code 请求中收到的 device_code。 |
| grant_type   | string | 必需。 授权类型必须是 urn:ietf:params:oauth:grant-type:device_code。 |

默认情况下，响应采用以下形式：

```
access_token=gho_16C7e42F292c6912E7710c838347Ae178B4a&token_type=bearer&scope=repo%2Cgist
```

如果在 Accept 标头中提供格式，则还可以接收不同格式的响应。 例如 Accept: application/json 或 Accept: application/xml：

```
Accept: application/json
{
 "access_token": "gho_16C7e42F292c6912E7710c838347Ae178B4a",
  "token_type": "bearer",
  "scope": "repo,gist"
}
```

```
Accept: application/xml
<OAuth>
  <access_token>gho_16C7e42F292c6912E7710c838347Ae178B4a</access_token>
  <token_type>bearer</token_type>
  <scope>gist,repo</scope>
</OAuth>
```
