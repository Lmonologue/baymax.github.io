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

近期调试一款基于mt7688名为widora的路由器开发板，发现openwrt下的一个很好用的TCP-UART互转插件--`ser2net`，经测试，该插件在Nanopi&Ubuntu也可使用

### 配置步骤

- 连接可上网的网络，安装插件<br>
`$ widora_mode repeater root 123456789`   
`$ opkg update`#升级安装包  
`$ opkg install ser2net`#安装ser2net 

- 配置网络<br>
`$ vn`<br>
```
config interface 'lan'
        option ifname 'eth0'
        option force_link '1'
        option macaddr '0c:ef:af:d0:49:77'
        option type 'bridge'
        option ipaddr '192.168.0.34'
        option netmask '255.255.255.0'
        option ip6assign '60'
        option proto 'static'
```
>注：添加网络配置，设置静态IP，中继模式只有LAN域，IP必须配置为与上级wifi相同的网段<br>
>如上级摄像头wifi每次分配的是0段,那么IP必须也是0段，如192.168.0.34<br>

- 查看要使用的串口<br>
`$ ls /dev/`#查看要使用的串口名，如ttyS0等 <br>`$ echo "hello" > /dev/ttyS1`#测试串口1是否正常		

- 配置ser2net<br>
`$ vi /etc/ser2net.conf`#配置ser2net转发	

```
#配置格式说明
	<TCP port>:<state>:<timeout>:<device>:<options>

TCP port：TCP/IP端口号(可以加IP,如127.0.0.1,2000或者localhost,2000)
state：   四种可选状态
	  off: 禁止该端口的连接
	  raw: 端口和串口设备之间双向通信
	  rawlp: 端口向串口设备单向通信
	  telnet: 使用telnet协议时用
timeout：超时，以秒为单位，当没有活动的连接时，可以设置这个时间关闭端口，即没有数据传输时，该端口自动关闭
	 常写0，关闭该功能，即不会超时
device： 指定映射本机的哪个串口（This must be in the form of /dev/<device>）
options: 设置串口的参数如：波特率（300，1200，2400，4800，9600，19200，38400，57600，115200）
	 校验（EVEN，ODD，NONE）
	 停止位（1STOPBIT,2STOPBITS)
	 数据位（7DATABITS，8DATABITS）
	 开启（关闭）XON\XOFF ：XONXOFF（-XONXOFF）
	 开启（关闭）硬件控制流：RTSCTS（-RTSCTS)
```
>#其中添加或者修改如下：<br>
>`2002:raw:600:/dev/ttyS1:115200 NONE 1STOPBIT 8DATABITS XONXOFF LOCAL -RTSCTS`<br>
>注：此处最好是屏蔽掉其他设备<br>

- 添加为开机启动<br>
`$ vi /etc/rc.local`<br>
`$ ser2net -c /etc/ser2net.conf`<br>

- 配置无线模式<br>
`$ vi /etc/Wireless/mt7628_tpl.dat`<br>
>WirelessMode=4G 1B 9N<br>

- 查看进程<br>
`ps -w`<br>





