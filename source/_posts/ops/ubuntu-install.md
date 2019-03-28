---
title: Windows10 安装 ubuntu-18.04.2
tags:
  - 运维
  - Ubuntu
date: 2019-03-28 17:34:26
---

# 下载 ubuntu 镜像

> 官方产品中心：<https://www.ubuntu.com/download>

在产品中心有很多类型的镜像，其中 `Desktop` 是桌面版，就像我们日常用的 `windows10` 一样是带用户界面的；`Server` 是服务器版本，就是黑窗口模式的，要操作的话使用命令；然后还有 `loT` 物联网版本等等，本人没有使用过就不过多介绍了。

在这篇文章中我将会以 `Server` 版本进行安装，谁叫我是一只 🐒。

下载链接：<http://mirrors.ustc.edu.cn/ubuntu-releases/18.04.2/ubuntu-18.04.2-live-server-amd64.iso>

> 下载慢的话可以参考 [下载工具](https://www.jianshu.com/p/4617d6050ff6) 文章中的方法使用迅雷下载。

# 开启 Hyper-V

> 官方教程：<https://docs.microsoft.com/zh-cn/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v>

## 方法一

1. `win + q`：搜索 `Hyper` 一般会出来 `启用或关闭 windows 功能`，如果没有的话就打开 `控制面板` 选择 `程序和功能`，在左侧也有该选项。
2. 点击进入 `启用或关闭 windows 功能`面板，在列表中选中 `Hyper-V` 选项，系统设置完之后会提示重启。
3. 重启电脑。

## 方法二

1. 以管理员身份打开 PowerShell 或 CMD 会话。
2. 键入下列命令：

```
DISM /Online /Enable-Feature /All /FeatureName:Microsoft-Hyper-V
```

3. 重启电脑。

# 创建虚拟机

> 官方教程：<https://docs.microsoft.com/zh-cn/virtualization/hyper-v-on-windows/quick-start/quick-create-virtual-machine>

- [Windows 10 Fall Creators Update 和更高版本](https://docs.microsoft.com/zh-cn/virtualization/hyper-v-on-windows/quick-start/quick-create-virtual-machine#windows-10-fall-creators-update)
- [Windows 10 创意者更新](https://docs.microsoft.com/zh-cn/virtualization/hyper-v-on-windows/quick-start/quick-create-virtual-machine#windows-10-creators-update)
- [Windows 10 周年更新和更早版本](https://docs.microsoft.com/zh-cn/virtualization/hyper-v-on-windows/quick-start/quick-create-virtual-machine#before-windows-10-creators-update)

接下来我讲以 `Windows 10 Fall Creators Update` 版本进行安装，基本也是按照官方的来；为了让大家少点下鼠标，我就把教程拷贝过来了。

## Windows 10 Fall Creators Update

在 Fall Creators Update 中，“快速创建”进行了扩展，以包括可以独立从 Hyper-V 管理器中启动的虚拟机库。
若要在 Fall Creators Update 中创建新虚拟机，请执行以下操作：

1. 从“开始”菜单中打开“Hyper-V Quick Create”。
   ![](http://upload-images.jianshu.io/upload_images/11353298-39ebc800b9e09d53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 提示：如果启用 `Hyper-V` 之后在 `win + q` 快速搜索没有出现 `Hyper-V` 程序的话，可以按 `win` 打开 `开始菜单`，然后 `windows 管理工具` 菜单中会有。

2. 选择一个操作系统或者使用本地安装源选择你自己的操作系统。
   ![](http://upload-images.jianshu.io/upload_images/11353298-932ca095b59165f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 基于国内的网络原因，打开 `Hyper-V 快速创建` 之后可能需要等程序联网去请求操作系统列表。

3. 选择 **_本地安装源_** 。
4. 选择 **_更改安装源_** 。
5. 取消选择 “此虚拟机将运行 Windows（启用 Windows Secure Boot）” 选项。
6. 展开 **_更多选项_** ，把名称修改为 `ubuntu` 或者选择默认。
7. 选择 “创建虚拟机” 。
8. 安装成功之后如图：

![](https://upload-images.jianshu.io/upload_images/11353298-74485dfb0f53e139.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 虚拟机基本设置

1. 点击 **_编辑设置_** 。

![](https://upload-images.jianshu.io/upload_images/11353298-8f95dab76b8b5c3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.  根据自己的需求分别设置 `内存` 、`处理器` 的配置（我的在上面图中）；其他的使用默认的就好了，主要是我了解的也不深 😇 。
3.  点击 **_确定_** 返回，点击 **_连接_** ，最后 **_启动_** ，大功告成 。

# 网络设置

> 虚拟机网络模式：`桥接` 、`NAT` 、`Host-Only`，详细资料请参阅：https://www.cnblogs.com/ggjucheng/archive/2012/08/19/2646007.html

打开 `Hyper-V 管理器` 在左侧栏中可以看到我们刚刚创建的虚拟机组，右键菜单中选择 `虚拟交换机管理器` 进入交换机设置界面。、
![](https://upload-images.jianshu.io/upload_images/11353298-75fcbfd428dab361.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/11353298-ba5d363f114da043.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到 `新建虚拟网络交换机` 设置中有三个选项：

1. 外部：对应 `桥接` 模式，即 `本地物理网卡` 与 `虚拟网卡` 进行桥接（$\color{red}{好比两个路由器使用桥接模式进行扩展一样}$），连接在 `虚拟交换机` 中的设备跟现实中的设备共用同一网段。
2. 内部：对应 `NAT` （网络地址转换）模式，即虚拟机使用主机的网络来访问外界网络，但是外部的网络是无法直接访问的，需要 `ssh` 到 主机再跳转过去。
3. 专用：对应 `Host-Only` 模式，虚拟机只能访问主机，无法访问外部网络。

接着往下设置，我们选中 `外部`，然后点击 `创建虚拟交换机`：
![](https://upload-images.jianshu.io/upload_images/11353298-0608edf4fc517e9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来设置我们之前创建的 'ubuntu 虚拟机'：
![](https://upload-images.jianshu.io/upload_images/11353298-fb56ec495791157a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/11353298-aeddfb68608a72e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

到此我们的网络设置就结束了，那为什么要设置呢？因为 `默认交换机` 使用的是 `NAT` 模式，我们无法使用 `xShell` 等工具进行远程。

# ubuntu 配置

只挑了一部分，看图说话：

![](https://upload-images.jianshu.io/upload_images/11353298-6546ed92fc2f43e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/11353298-684a978680b90abe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/11353298-fdce39e72bfbe485.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/11353298-e0926579ece3bb3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/11353298-03a2dab0673d0eaf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 第二张图没截好，这里解释一下 `server's name`：一般我们 ssh 到一台 `Linux` 服务器的话，命令窗口都会有当前登录信息：`username@host`；
> 图中的 `server's name` 其实就是 `@` 后面的 `host`。
