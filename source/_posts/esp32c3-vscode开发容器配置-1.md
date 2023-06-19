---
title: esp32c3-vscode开发容器配置-1
date: 2023-06-19 12:28:29
categories:
tags:
    - esp32
    - 嵌入式
---

运行esp32示例程序时注意到右下角有"ESP-IDF QEMU"按钮，好奇，就点了一下，发现这个是与docker开发容器关联的，乐鑫提供了一套docker镜像来进行esp32开发。

在vs code中要先安装"dev container"插件，然后这个插件会自动检测系统环境，完成docker安装。

但是示例程序中的开发容器配置有问题：

<!-- more -->

```json
// For format details, see https://aka.ms/devcontainer.json. For config options, see the README at:
// https://github.com/microsoft/vscode-dev-containers/tree/v0.183.0/containers/ubuntu
{
	"name": "ESP-IDF QEMU",
	"build": {
		"dockerfile": "Dockerfile"
	},
	// Add the IDs of extensions you want installed when the container is created
	"workspaceMount": "source=${localWorkspaceFolder},target=${localWorkspaceFolder},type=bind",
	/* the path of workspace folder to be opened after container is running
	 */
	"workspaceFolder": "${localWorkspaceFolder}",
	"mounts": [
		"source=extensionCache,target=/root/.vscode-server/extensions,type=volume"
	],
	"customizations": {
		"vscode": {
			"settings": {
				"terminal.integrated.defaultProfile.linux": "bash",
				"idf.espIdfPath": "/opt/esp/idf",
				"idf.customExtraPaths": "",
				"idf.pythonBinPath": "/opt/esp/python_env/idf5.1_py3.8_env/bin/python",
				"idf.toolsPath": "/opt/esp",
				"idf.gitPath": "/usr/bin/git"
			},
			"extensions": [
				"ms-vscode.cpptools",
				"espressif.esp-idf-extension"
			],
		},
		"codespaces": {
			"settings": {
				"terminal.integrated.defaultProfile.linux": "bash",
				"idf.espIdfPath": "/opt/esp/idf",
				"idf.customExtraPaths": "",
				"idf.pythonBinPath": "/opt/esp/python_env/idf5.1_py3.8_env/bin/python",
				"idf.toolsPath": "/opt/esp",
				"idf.gitPath": "/usr/bin/git"
			},
			"extensions": [
				"ms-vscode.cpptools",
				"espressif.esp-idf-extension"
			],
		}
	},
	"runArgs": ["--privileged"]
}


<!-- more -->

运行时会提示这个配置文件中的工作区目录应为绝对路径，修改如下：

```json
// For format details, see https://aka.ms/devcontainer.json. For config options, see the README at:
// https://github.com/microsoft/vscode-dev-containers/tree/v0.183.0/containers/ubuntu
{
	"name": "ESP-IDF QEMU",
	"build": {
		"dockerfile": "Dockerfile"
	},
	// Add the IDs of extensions you want installed when the container is created
	"workspaceMount": "source=E:\\work\\esp32-test\\hello_world,target=/home/peter/esp32-test/hello_world,type=bind",
	/* the path of workspace folder to be opened after container is running
	 */
	"workspaceFolder": "/home/peter/esp32-test/hello_world",
	"mounts": [
		"source=extensionCache,target=/root/.vscode-server/extensions,type=volume"
	],
	"customizations": {
		"vscode": {
			"settings": {
				"terminal.integrated.defaultProfile.linux": "bash",
				"idf.espIdfPath": "/opt/esp/idf",
				"idf.customExtraPaths": "",
				"idf.pythonBinPath": "/opt/esp/python_env/idf5.1_py3.8_env/bin/python",
				"idf.toolsPath": "/opt/esp",
				"idf.gitPath": "/usr/bin/git"
			},
			"extensions": [
				"ms-vscode.cpptools",
				"espressif.esp-idf-extension"
			],
		},
		"codespaces": {
			"settings": {
				"terminal.integrated.defaultProfile.linux": "bash",
				"idf.espIdfPath": "/opt/esp/idf",
				"idf.customExtraPaths": "",
				"idf.pythonBinPath": "/opt/esp/python_env/idf5.1_py3.8_env/bin/python",
				"idf.toolsPath": "/opt/esp",
				"idf.gitPath": "/usr/bin/git"
			},
			"extensions": [
				"ms-vscode.cpptools",
				"espressif.esp-idf-extension"
			],
		}
	},
	"runArgs": ["--privileged"]
}
```

这样配置就没问题了，但运行还是有问题：

<iframe src="//player.bilibili.com/player.html?bvid=BV1mV4y117ur&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

这个主要就是网络问题了，科学不科学上网都试过了，但是没有解决。。。
