---
title: 威联通NAS安装docker容器
date: 发布时间 2023-7-26 00:16:26
categories:
  - 教程
tags:
  - 威联通
  - NAS
  - docker
  - container station
  - 容器
description: 基于威联通5.1.0.2466系统版本讲解怎么在container station（docker）下部署安装容器。
cover: https://s2.loli.net/2023/07/25/jdbaMFZs47W5E6A.png
---

## nastool介绍

NAS Tools是一款官方称之为『NAS媒体库资源归集整理工具』的软件。它可以自动罗列出所有可用的电影资源，让你轻松调用下载器进行下载。
下载完成后，配合媒体服务器如Jellyfin进行影片刮削，展示完美的电影海报墙，让你可以随时观影、追剧。
此外，NAS Tools还可以帮助你整理影音资源，进行重命名和管理。它的功能整合了Jackett索引器、qBittorrent下载器和Jellyfin媒体服务器，让观影追剧变得自动化，非常方便。
此外，NAS Tools还提供了消息服务，可以进行远程下载、签到、同步等操作。总之，如果你喜欢收藏电影、追剧和观影，NAS Tools是一个非常值得推荐的工具。

![nastools展示](https://s2.loli.net/2023/07/26/BjWoHwO7aSYFR6D.png) 

## 安装前准备

在安装NAS Tools之前，需要打开File Station文件总管，并在Container文件夹中创建一个名为nastools的文件夹。在该文件夹中，再创建一个名为config的文件夹。

## nastools安装

### 正常从容器商城中安装

打开Container Station，搜索NAS-Tools，在Docker Hub一栏找到jxxghp/nas-tools或者nastool/nas-tools，点击部署进行安装。

![商城安装](https://s2.loli.net/2023/07/26/wKVQh8XExU2NTbf.png)

安装了NAS Tools之后，你可以使用它来进行硬链接或跨盘符复制转移，并进行媒体库刮削。

### 映像安装

![版本选择](https://s2.loli.net/2023/07/26/XiyPcRSbADnWMg3.png)
如果你发现从容器商城中部署无法选择版本那么接下来你需要这样做
选择映像→提取→高级模式→映像，映像栏就是容器作者名/容器名:版本（普遍latest就可以），提取就可以提取到映像列表。
![映像提取流程](https://s2.loli.net/2023/07/26/ubgQiz3BXToWNcn.png)
如果你输入的影像名字与docker hub里的不吻合，那肯定是提取不到的。没关系，我教你怎么正确找到容器名字，**看下图**：

![获取容器名](https://s2.loli.net/2023/07/26/AZ2iCcad5t7mwfv.png)

这样就能看到顺利提取容器啦。

![顺利提取](https://s2.loli.net/2023/07/26/sx1cPyf4eGMSdOI.png)

选择映像列表开始键（黑色小三角）进行配置创建。

### 导入安装

从容器商城或者docker官网下载映像文件，格式可以是*.tar、*.tar.gz、*.tgz等，这里就不做细说了。

## nastools配置

![image.png](https://s2.loli.net/2023/07/26/oGPFMjBxg8SLQl7.png)

一般采用HOST模式，就是本地IP即nasIP加端口号直接登录nastools

![image.png](https://s2.loli.net/2023/07/26/69R5z73Z1rtYSla.png)

专门单列一个卷做影音卷的话，就要要映射2个视频卷，一个video映射下载的源文件，一个复制的目的文件夹link（对应emby媒体库），但是这样的话就无法最硬链接了，要做硬连接，下图只映射一个文件夹。

![image.png](https://s2.loli.net/2023/07/26/E9ZpPDT8VdjghsJ.png)

## 登录nastool

![image.png](https://s2.loli.net/2023/07/26/3x5sVQiCA18hJ96.png)

完成配置后，在浏览器输入nas地址加容器端口号进行访问了，输入默认用户名admin，密码：password就可以使用容器啦，恭喜大家，可以在威联通nas上使用nastools啦。

![image.png](https://s2.loli.net/2023/07/26/d8kzSMZOhaWATt3.png)
