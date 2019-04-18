---
title: win10 下 ctrl space 组合键被输入法占用
date: 2019-04-08 17:20:48
tags:
  - Windows
---

## 问题描述

在 win10 系统下，ctrl space 组合键被默认输入法注册为切换功能的热键，导致很多 IDE 的智能提示功能无法使用。

## 解决方案

1. 在 `文本服务和输入语言 -> 高级键设置` 控制面板下取消，重启被还原，该方法无效。
2. 下载 {% ctrl-space/ctrl-space-fixed.bat slug %}，然后注销或重启电脑。详情：[如何改变 Windows 默认的输入法快捷键 Ctrl+Space 为其他值？](https://www.zhihu.com/question/22288432)
