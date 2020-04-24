---
title: "Anaconda 使用清华源并解决警告"
date: 2020-04-23T11:09:19+08:00
draft: false
tags: ["应用软件"]
categories: ["软件系统"]
---

相信只要是写过 Python 的人，对于 Anaconda 一定不陌生。Anaconda 默认附带了很多科学计算常用的包，并且提供了方便的虚拟环境隔离。但是，由于国内众所周知的网络问题，如果使用默认源，安装之后无论是更新还是安装新包都会遇到很多困难。当然，你肯定会说，换源呗，而在国内 Anaconda 使用最广泛的源就是清华大学 TUNA 源。不过，在实际操作过程中中，我发现了一些问题，并经过摸索顺利解决，现将解决方案分享如下。

<!--more-->

### 问题描述

当你安装好 Anaconda，按照 [TUNA 官网](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/) 的使用帮助修改好了 `.condarc` 文件，并清除了索引之后，想要更新包，输入 `conda update --all` 时，你会发现软件会发出如下的警告：

> Warning: >10 possible package resolutions (only showing differing packages):

也就是说不同源内的包出现了冲突。我开始以为是 Anaconda 默认的安装的包的问题，我便从官网下载了 Miniconda ，可是问题依然没有解决。虽然你可以无视这些警告继续完成更新，但是既然报错便应当想办法去解决，我便去寻找解决方案。

### 解决思路

经过查阅资料，我发现按照 TUNA 的 .condarc 配置，虽然 channel 里貌似没有默认的 http://repo.anaconda.org channel，然而 Anaconda 在更新时还是会尝试从默认 channel 中下载，这样就发生了冲突。那么解决思路就很显然了，从 channel 列表中彻底删除默认 channel 。

### 最终方案

经过尝试，我发现只需直接在 `channels` 字段把所有需要用到的源列出就可以了，不需要整一些花里胡哨的骚操作。清华提供的配置文件配置了一些 alias 之类的内容，反而出了问题。现将我修改后的 `.condarc` 文件分享如下：

```
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
show_channel_urls: true
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
```

### 附录：Anaconda 代理的设置

当然，你也可以不换源而采用代理的方式来解决。Anaconda 默认会读取系统代理设置，而为 Anaconda 单独设置代理也很简单，参照[官方文档](https://docs.anaconda.com/anaconda/user-guide/tasks/proxy/)，只需在 .condarc 中加入如下内容（代理端口自行修改）

```
proxy_servers:
    http: http://127.0.0.1:port
    https: https://127.0.0.1:port
```

之后，conda 便会以代理联网了。