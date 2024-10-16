---
title: 利用DNSPod实现DDNS
date: 2024-09-13 11:24:53
categories:
tags:
    - 工具
---


在前一篇文章 [聊聊DNS](https://mp.weixin.qq.com/s?__biz=MzA3NzMyNTIyOA==&mid=2651482734&idx=1&sn=c7359cc4d4c0346f98fde0bbe358ccea&chksm=84ad7cadb3daf5bb20dc3f1b76cc5c1306763f4232b7a3e06e764ccb7b171a830debed595a68#rd) 中已经大致聊了DDNS的一些基本步骤。本文以DNSPod为例，介绍如何利用DNSPod的API实现DDNS。

> 本教程仅适用于解析托管在DNSPod平台上的域名，其他平台托管的域名请自行通过搜索引擎查找教程。

> 有需要自定义域名访问自己开发服务器的朋友可以联系我，省得自己再折腾域名了。

## DDNS是什么

对于DHCP方式获得的IP，无论对于局域网内来说，还是外网来说，都会有使得IP地址每隔一段时间变化一次，如果想要通过恒定不变的地址访问主机，就需要动态域名解析。用一句话说明原理是：把动态变化的IP地址绑定到固定不变的域名上，这样便是以不变应万变，只要记住域名就可以了。

<!-- more -->

## 什么时候需要用？

应用场景举例：

1. 在公司局域网内，小明在自己的电脑上建立了一个FTP服务器用来共享文件。但他使用普通的上网账号上网，主机接入局域网后需要认证账号，随机分配IP地址。每次断线之后都会使得IP地址发生变化，这样一来，小明需要时刻记录下当前的IP地址，才能通过该地址访问到FTP服务器。做动态域名解析后，主机IP绑定到小明自己的域名，一旦IP地址发生变化，更新域名解析内容，他依旧可以通过域名访问到FTP服务器。 

2. 阿杰办理了电信宽带后幸运地发现，获得的IP可以从外网直接访问(之所以说他幸运，是因为现在IPV4资源紧张，宽带运营商都是做了一层NAT再后在给用户分配内网IP，很多人实际公用一个出口IP. )。阿杰于是在自己的主机上搭了一个IP摄像头，这样无论在何地都可以通过网络查看家里的情况。IP虽然是公网IP，但却是动态分配的(公网静态IP要10万块钱一年)，每断开一次就会变化一次。因此他可以选择动态域名解析。

## DDNS可以解决什么？ 

可以把变化的IP自动动态地映射到域名上，通过不变的域名访问变化IP的主机；

## DDNS不能解决什么？

本身不能实现端口映射，因此如果主机在NAT网络内部，而你又没有权限在路由器上设置端口映射，那么DDNS就无解了，需要做反向代理或者端口穿透。

## 怎么做？

根据能力和预算有不同的做法：

不差钱可以直接去花生壳购买动态域名服务，顺便还能提供内网穿透的功能；

自己折腾可以使用DNSPod + 个人域名 + ArDNSPod脚本，最后一项看个人偏好，完全可以自己写一个脚本实现。另外也有一些可以安装在智能路由或软路由上的插件，可以实现DDNS。

## 利用DNSPod实现动态域名解析

首先需要注册一个域名，去腾讯云注册最为方便，因为DNSPod 2015年被腾讯收购后和腾讯云的账号打通了。

登陆[dnspod控制台](https://console.dnspod.cn/dns/list) https://console.dnspod.cn/dns/list，创建一条A记录解析，IP值暂时随便填就行：

![添加记录](https://ask.qcloudimg.com/http-save/yehe-8223537/4310b7b8eb03752a348c5c086d3b4a92.png)

在 [dnspod 的管理界面](https://console.dnspod.cn/account/token#) https://console.dnspod.cn/account/token#  创建API Token 

![创建token](https://ask.qcloudimg.com/http-save/yehe-8223537/1ce85a5a1a19bb1a943d32fce8a24fd6.jpeg)

有了以上两个信息，就可以利用DNSPod的API实现DDNS了。

1. 接口描述

本接口 Record.Ddns 用于更新动态 DNS 记录。

接口请求地址：https://dnsapi.cn/Record.Ddns

HTTP请求方式：POST

2. 请求参数

以下请求参数列表仅列出了接口请求参数，正式调用时需要加上公共请求参数，见 [公共请求参数](https://docs.dnspod.cn/api/5f561f9ee75cf42d25bf6720/) 页面。

| 参数名称            | 是否必选 | 类型              | 描述                                                                                                                               |
| ------------------- | -------- | ----------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| domain_id 或 domain | 是       | Integer 或 String | 分别对应域名ID和域名, 提交其中一个即可。                                                                                           |
| record_id           | 是       | Integer           | 记录ID。                                                                                                                           |
| sub_domain          | 否       | String            | 主机记录，如 www。                                                                                                                 |
| record_line         | 是       | String            | 记录线路，通过API记录线路获得，中文。比如：默认。                                                                                  |
| record_line_id      | 是       | String            | 线路的ID，通过API记录线路获得，英文字符串，比如：‘10=1’。record_line 和 record_line_id 二者传其一即可，系统优先取 record_line_id。 |
| value               | 否       | String            | IP 地址，支持 IPv4、IPv6，例如 1.2.3.4 或者 ff06::c3                                                                               |
| ttl                 | 否       | Integer           | TTL值，例如：600                                                                                                                   |


3. 响应代码

以下参数列表仅列出了此接口响应代码，除此接口请求响应代码外。还包含 [共通返回](https://docs.dnspod.cn/api/5f561f1de75cf42d25bf6710/)。

| 响应码 | 描述                        |
| ------ | --------------------------- |
| -15    | 域名已被封禁。              |
| 6      | 域名ID错误。                |
| 7      | 非域名所有者。              |
| 8      | 域名无效。                  |
| 17     | 记录的值不正确。            |
| 21     | 域名被锁定。                |
| 22     | 子域名不合法。              |
| 23     | 子域名级数超出限制。        |
| 24     | 泛解析子域名错误。          |
| 500025 | A记录负载均衡超出限制。     |
| 500026 | CNAME记录负载均衡超出限制。 |
| 26     | 记录线路错误。              |

4. 示例

```shell
curl -X POST https://dnsapi.cn/Record.Ddns -d 'login_token=LOGIN_TOKEN&format=json&domain_id=2317346&record_id=16894439&record_line_id=10%3D0&sub_domain=www'
```

返回示例如下：

```json
{
    "status": {
        "code":"1",
        "message":"Action completed successful",
        "created_at":"2015-01-18 17:23:58"
    },
    "record": {
        "id":16909160,
        "name":"@",
        "value":"111.111.111.111"
    }
}
```

字段说明:

id: 记录ID, 即为 record_id

name: 子域名

value: 记录值

> 注意：record_line_id 形如 “10=3”，其中可能会包含等号，即 “=”，如果是通过类似 URL 传递参数，需要将 ‘=’ 转义成 ‘%3D’。