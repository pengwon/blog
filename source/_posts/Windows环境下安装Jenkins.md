---
title: Windows环境下安装Jenkins
date: 2022-11-16 15:15:43
tags:
    - powershell
    - ci/cd
    - jenkins
---

> 这篇文章之前发布在[稀土掘金](https://juejin.cn/post/7146204009928327182)上，所以图片上有水印。

今天在win10上安装了Jenkins，作为团队CI/CD服务器，中间有些小坑，记录下过程。

## 环境
1. Windows10 专业工作站版 19044.2006
2. Jenkins 2.361.1
> 注意：这个版本的Jenkins只能用Java11或17
3. Java11 jdk-11.0.16.1

<!-- more -->

## 安装
1. 双击"jenkins.msi"
2. 常规"Next"

![安装向导界面](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/87891f0921534365928b5a95c644e764~tplv-k3u1fbpfcp-watermark.image?)

3. 选择安装目录，通常默认就可以

![安装向导界面](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/23c9013d137b4a6caf762873dc490ca3~tplv-k3u1fbpfcp-watermark.image?)

4. 这一步比较重要了，可以添加为本地系统服务，但是并不建议，推荐运行以用户登录运行为本地服务。


![安装向导界面](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ce3e16f307249ea889a1dc2a1e4f7d5~tplv-k3u1fbpfcp-watermark.image?)

5. 具体操作，Win键搜索“本地安全策略”，打开


![安装向导界面](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7626cf4ba1834cd08e374359f46a3617~tplv-k3u1fbpfcp-watermark.image?)

6. 本地安全策略 → 安全设置 → 本地策略 → 用户权限分配 → 作为服务登录，在其中添加一个用户，通常就当前登录用户就可以


![安装向导界面](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/986c6e2eda784d7badbf3509bce70eec~tplv-k3u1fbpfcp-watermark.image?)

7. 然后在Jenkins安装向导中填上用户名、密码，测试验证，Next
8. 设置端口，默认8080，测试通过，Next
9. 选择JDK或JRE，这里需要指向包含"bin"的目录才能正常识别
10. 安装选项，这里可以把防火墙选项也选上，Next


![安装向导界面](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f6c55db9a9d4402da111401ace9cf332~tplv-k3u1fbpfcp-watermark.image?)

12. Install
13. 安装完成

正常情况下应该还是比较顺利的。

## 问题解决
1. 如果不是初次安装，可能会遇到卡在安装过程启动服务（start service）的问题

> 遇到这个问题可以打开Windows服务管理（Win键搜“服务”），找到“jenkins”，点进去在登录选项卡重新输下账号密码，再启动，就可以了。

2. 启动后只能本机访问，不能外部访问

> 这个有点奇怪，正常防火墙放行应该可以的，但还是遇到这个问题。打开本地安全策略（方法同安装步骤6），高级安全··· → 高级安全··· → 入站规则，在右边空白处右键新建规则，将8080（安装步骤中设置的）端口放行，就可以了。

3. 还有个不算问题的问题，修改Jenkins默认数据目录位置

> 这个有点走弯路，开始时搜了一圈，网上给出的通常都是什么设置系统环境变量"JENKINS_HOME"的方法，但是我照着做完并没有生效，可能是不同版本有差异。后来想到这个可能是通过启动配置文件设置的，就去安装目录下找，发现有一个"jenkins.xml"文件，打开后果不其然，里边有一行env:JENKINS_HOME，是在这里设置的，如果想修改默认数据目录，修改这里，再重新启动jenkins服务就可以了。其实设置系统环境变量也是可以的，但是要把"jenkins.xml"文件这一行屏蔽掉才行。
> 后来再看Jenkins网站时发现其实这个操作是有提示说明的：


> ### Changing boot configuration
> By default, your Jenkins runs at [https://localhost:8080/. ](http://localhost:8080/)This can be changed by editing `jenkins.xml` , which is located in your installation directory. This file is also the place to change other boot configuration parameters, such as JVM options, HTTPS setup, etc.


> ### Starting/stopping the service
> Jenkins is installed as a Windows service, and it is configured to start automatically upon boot. To start/stop them manually, use the service manager from the control panel, or the `sc` command line tool.
> 
> 推荐使用[PowerShell 7](https://github.com/PowerShell/PowerShell)，搭配[Windows Terminal](https://github.com/microsoft/terminal)，真的非常好用！在powershell中用`start-service jenkins`、`stop-service jenkins`、`restart-service jenkins`来控制jenkins服务。

> ### Inheriting your existing Jenkins installation
> If you'd like your new installation to take over your existing Jenkins data, copy your old data directory into the new `JENKINS_HOME` directory.

> 注意：这里重启服务后，Jenkins会重新进入配置向导，按照提示操作就可以。
