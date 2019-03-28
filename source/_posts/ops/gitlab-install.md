---
title: Gitlab 使用指南 - 安装篇
tags:
  - 运维
  - gitlab
date: 2019-03-28 17:29:17
---

# 安装环境

> 详细的系统要求可以查看：https://docs.gitlab.com/ee/install/requirements.html

官方给出的是 $2核8GB$ 稳定支撑 $100用户$，一般我们使用 $2核4GB$ 也差不多够了。

操作系统

> 主机使用的是虚拟机：[Windows10 安装 ubuntu-18.04.2](https://www.jianshu.com/p/9282faf635be)

```
# 版本
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.2 LTS
Release:	18.04
Codename:	bionic

# 架构
Linux ubuntu 4.15.0-45-generic 48-Ubuntu SMP Tue Jan 29 16:28:13 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux

```
硬件信息

```

CPU: Intel(R) Xeon(R) CPU E3-1231 v3 @ 3.40GHz
内存:  3993580 kB ≈ 4GB

```
# 设置下载镜像源

> [清华大学开源软件镜像站](https://mirror.tuna.tsinghua.edu.cn/help/gitlab-ce/)，赞一个👍

## 小科普：

1. `sources.list`：常规的仓库文件，`apt-get install` 的时候会按照文件中的仓库源顺序查找下载。

2. `sources.list.d`： 仓库目录，安装软件的时候会把该目录下的配置信息合并到 `sources.list` 中，关于查找顺序本人暂时还不清楚。

   我们把每个安装包的配置源单独放在 `sources.list.d` 目录中有以下好处：

   a. 文件名自带备注，方便查找。
   b. 添加或者删除只需要删除对应的文件就好，没有副作用。
   c. 方便第三方安装脚本使用。

3.  配置文件说明：

```

deb                   https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu/                bionic                                       main 
包类型                 源（镜像站点）                                 版本类型                 版本号（codenmae）                            包许可类型

# 包类型
`deb`：二进制包，即下载下来平台可以直接使用的。
`deb-src`：源码包，即下载之后需要自己手动进行编译的。

#  版本号
bionic: Ubuntu 18.04.1 LTS，更多的可以查看：https://wiki.ubuntu.com/Releases

```

## 起步

首先信任 GitLab 的 GPG 公钥:

```

curl https://packages.gitlab.com/gpg.key 2> /dev/null | sudo apt-key add - &>/dev/null

```

新建 `Gitlab` 源文件：

```

sudo vim  /etc/apt/sources.list.d/gitlab-ce.list

```
把以下内容复制进去：

```

deb https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu/ bionic main

```

进行安装：

```

# 更新源
sudo apt update
# 安装 gitlab-ce
sudo apt install gitlab-ce

```

## 基础配置

> 详细配置可以参考官方文档：https://docs.gitlab.com/omnibus/settings/configuration.html。   

打开 `gitlab.rb` 配置文件

```

sudo vim /etc/gitlab/gitlab.rb

```

把 `external_url` 更改为你本地IP 或者配置的域名（不要忘记加 `http://`），默认端口一般为 `80`:

![](https://upload-images.jianshu.io/upload_images/11353298-3c4685569fecb3ba.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


修改完之后执行以下命令：

```

# 让 Gitlab 使用新配置
sudo gitlab-ctl reconfgiure

# stdout
> gitlab Reconfigured!
```
访问 `external_url`/help/instance_configuration 查看当前 `gitlab` 服务的实例配置信息。

## 结束

访问 `external_url`/help 看看是不是最新的（国外的🐒都是更新狂魔）：

![](https://upload-images.jianshu.io/upload_images/11353298-ad0af73c0261ed8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
