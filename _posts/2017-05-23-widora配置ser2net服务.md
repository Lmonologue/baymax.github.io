---
layout:         post
title:          "widora配置ser2net服务"
subtitle:       "ser2net"
date:           2017-05-23 
author:         "HJ."
header-img:     "img/post-bg-2017.jpg"
catalog:    true
tags:
    - openwrt
---

### 说明

近期调试一款基于mt7688的名为widora的一款路由器的开发板，发现openwrt下的一个很好用的TCP<->UART插件--`ser2net`，经测试，该插件在Nanopi&Ubuntu均可使用。

### 配置步骤

1. 连接可上网的网络，更新插件

`$ connect2ap root 123456789`
`$ opkg update`//升级安装包
`$ opkg install ser2net`//安装ser2net
