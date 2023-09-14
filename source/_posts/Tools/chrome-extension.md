---
title: lighthouse不好用？是否想过集成到自己的Chrome插件呢
comments: true
cover: cover.png
date: 2023-09-14 16:55:42
categories:
- 测试工具
keyword: lighthouse,Chrome插件,lighthouse二次开发
tags:
- 原创
---

在前文提过，lighthouse提供了3种方式让开发者去使用。但作者仍然觉得不够方便，理想情况下，作者希望通过一个按钮就能实现内网测试，并且针对同一个url有多个不同阶段的评分进行对比，这样每次调优都会有参照物。那么如何实现呢？
<!-- more -->

<font color='green' size='4'>Chrome插件</font>是我认为最优解决方案，原因如下：

1. Chrome extension本身可以调用chrome各种指令，比如获取当前页面路径和cookies等信息
2. 它能在网站的上层工具栏使用，避免对网站的干扰，你只需要一个按钮就能完成你想到的
3. Chrome extension开发也比较简单，目前有比较轻量且成熟的框架plasmo

# Chrome extension开发

# 集成lighthouse
