---
title: 公众号开放接口token验证
date: 2024-09-03 11:24:53
categories:
tags:
    - 工具
---


微信公众平台是运营者通过公众号为微信用户提供资讯和服务的平台，而公众平台开放接口则是提供服务的基础，开发者在公众平台网站中创建公众号、获取接口权限后，可以通过接口实现用户管理、素材管理、消息管理等功能。

最近在重构 [在线ADLib服务](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651482574&idx=1&sn=c10e90584e712b18b73796287c3f7dd2&chksm=84ad7f0db3daf61b284c754657658cc72e5c4beb3d66265b91f7e756938e2b4d4812bd040d38#rd) 这个项目，想用下公众号的接口，结果第一步就卡住了，特此记录下。

大多数情况下，调用接口服务都需要先获取access_token，而我主要想用的是接收公众号消息的接口，并不需要获取access_token，只绑定自己的服务器就可以。所以我的第一步卡在了绑定服务器上，离了大谱！

## 接入概述

接入微信公众平台开发，开发者需要按照如下步骤完成：

1. 填写服务器配置
2. 验证服务器地址的有效性
3. 依据接口文档实现业务逻辑

<!-- more -->

### 填写服务器配置

登录微信公众平台官网后，在公众平台官网的开发-基本设置页面，勾选协议成为开发者，点击“修改配置”按钮，填写服务器地址（URL）、Token和EncodingAESKey，其中URL是开发者用来接收微信消息和事件的接口URL。Token可由开发者可以任意填写，用作生成签名（该Token会和接口URL中包含的Token进行比对，从而验证安全性）。EncodingAESKey由开发者手动填写或随机生成，将用作消息体加解密密钥。

同时，开发者可选择消息加解密方式：明文模式、兼容模式和安全模式。模式的选择与服务器配置在提交后都会立即生效，请开发者谨慎填写及选择。加解密方式的默认状态为明文模式，选择兼容模式和安全模式需要提前配置好相关加解密代码。

![服务器配置](https://mmbiz.qpic.cn/mmbiz/PiajxSqBRaEIQxibpLbyuSK3AXezF3wer8dofQ1JMtIBXKX9HmjE1qk3nlG0vicvB55FVL5kgsGa5RgGKRc9ug87g/0?wx_fmt=png)

### 验证消息的确来自微信服务器

开发者提交信息后，微信服务器将发送GET请求到填写的服务器地址URL上，GET请求携带参数如下表所示：

![](https://imgs.boringhex.top/blog/20240903215815.png)

开发者通过检验signature对请求进行校验。若确认此次GET请求来自微信服务器，请**原样返回echostr参数内容**，则接入生效，成为开发者成功，否则接入失败。

![](https://imgs.boringhex.top/blog/20240903220631.png)

当你原样返回echostr参数内容后，神奇的事情发生了，页面会提示你token验证失败！！！

我反复试了很多次，json、xml各种试，都不行，最后发现echostr这个参数一直是数字，就试了下返回数字，然后就成功了。公开信息显示，根据今年上半年的数据推算，企鹅员工平均年薪突破了100w，我觉得应该是太忙了，没时间更新文档。

```python
@app.api_route("/wx_gzh/echo", methods=["GET", "POST"])
async def echo(
    request: Request,
    signature: str = None,
    timestamp: str = None,
    nonce: str = None,
    echostr: int = None,
):
    if request.method == "POST":
        headers = request.headers
        body = await request.body()

        # 打印请求头和请求体
        print("Headers:", headers)
        print("Body:", body.decode("utf-8"))

        # 解析XML请求体
        root = ET.fromstring(body)
        xml_data = {child.tag: child.text for child in root}

        return {"headers": dict(headers), "body": xml_data}

    elif request.method == "GET":
        token = "adlib"  # 替换为你的Token
        data = [token, timestamp, nonce]
        data.sort()
        data = "".join(data).encode("utf-8")

        hashcode = hashlib.sha1(data).hexdigest()
        if hashcode == signature:
            return JSONResponse(content={"echostr": echostr})
        else:
            return JSONResponse(content={"error": "Invalid signature"}, status_code=400)
```

不过当天晚上拿到了GitHub Models的试用资格，哈哈！现在确实离了GitHub Copilot已经不知道怎么写代码了，看看Github Models能带来什么惊喜。

![](https://imgs.boringhex.top/blog/9f6ebf53349db31f4f241f69d47ea19.jpg)