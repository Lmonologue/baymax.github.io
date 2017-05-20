---
layout:         post
title:          "win7使用python修改PC的IP地址"
subtitle:       "python修改IP、ping"
date:           2017-05-11 22:00:00
author:         "HJ"
header-img:     "img/post-bg-2017.jpg"
catalog:        true
tags:
    - python
---

### 前言

由于大白实验室和其他实验室共用同一路由下的IP网段，可能设备年老未修，可以用的IP地址实在少得可怜，并且还容易被他人抢占。为了解决这个问题，找到可以使用的IP，大白结合网上资源凑了一个python的demo.py，可以修改PC的IP为输入的IP范围，然后通过ping网关判断该IP是否可用，从而得到可以使用的IP

```python
import os
import sys
import wmi
import time

print 'Changing IP addr...' 

wmiService = wmi.WMI()
colNicConfigs = wmiService.Win32_NetworkAdapterConfiguration(IPEnabled = True) 

if len(colNicConfigs) < 1:
    print 'No network adapter'
    exit()
objNicConfig = colNicConfigs[0] 

arrIPAddresses = ['121.194.93.83']
arrSubnetMasks = ['255.0.0.0']
arrDefaultGateways = ['121.194.93.254']
arrGatewayCostMetrics = [1]
arrDNSServers = ['202.204.105.1']
intReboot = 0 

def ChangeIP(arrIPAddresses):

    returnValue = objNicConfig.EnableStatic(IPAddress = arrIPAddresses, SubnetMask = arrSubnetMasks) 
    if returnValue[0] == 0:
        print 'Set IP is succeed!'
    elif returnValue[0] == 1:
        print 'Set IP is succeed!'
        intReboot += 1
    else:
        print 'Set IP is error!'
        exit()

    returnValue = objNicConfig.SetGateways(DefaultIPGateway = arrDefaultGateways, GatewayCostMetric = arrGatewayCostMetrics)
    if returnValue[0] == 0:
        print 'Set gatway is succeed!'
    elif returnValue[0] == 1:
        print 'Set gatway is succeed!'
        intReboot += 1
    else:
        print 'Set gateway is error!'
        exit() 

    returnValue = objNicConfig.SetDNSServerSearchOrder(DNSServerSearchOrder = arrDNSServers)
    if returnValue[0] == 0:
        print 'Set DNS is succeed!'
    elif returnValue[0] == 1:
        print 'Set DNS is succeed!'
        intReboot += 1
    else:
        print 'Set DNS is error!'
        exit()
'''        
    if intReboot > 0:
        print 'Need reboot pc'
    else:
        print 'After modify:'
        print 'IP:',','.join(objNicConfig.IPAddress)
        print 'Mask:',','.join(objNicConfig.IPSubnet)
        print 'Gateway:',','.join(objNicConfig.DefaultIPGateway)
        print 'DNS:',','.join(objNicConfig.DNSServerSearchOrder)
    print 'Modify IP is completed!'
'''

IPbegin = raw_input(u'Input start IP:')
IPend = raw_input(u'Input stop IP:')

IP1 =  IPbegin.split('.')[0]
IP2 =  IPbegin.split('.')[1]
IP3 =  IPbegin.split('.')[2]
IP4 = IPbegin.split('.')[-1]

IPend_last = IPend.split('.')[-1]

ip_True = open('ip_True.txt','w+')
ip_False = open('ip_False.txt','w+')

dstip='202.204.105.195'

for i in range(int(IP4)-1,int(IPend_last)):
    ip = str(IP1+'.'+IP2+'.'+IP3+'.'+IP4)
    int_IP4 = int(IP4)
    int_IP4 += 1
    IP4 = str(int_IP4)
    print ip
    
    ChangeIP([ip])

    time.sleep(8)

    res=os.system('ping -n 1 -w 1 %s'%dstip)
    if res:
        print '%s ping is fail'%ip
        ip_False.write(ip+'\n')
    else:
        print '%s ping is ok'%ip
        ip_True.write(ip+'\n')
    print '****************************************************'

```

### 说明

- 该py必须要在管理员权限下运行，先以管理员权限打开cmd窗口，然后进入该脚本所在目录，最后运行`python demo.py`
- 该py脚本需要安装wmi库
  安装方法：离线下载->放入py安装目录->解压->进入解压目录->shift+右键打开命令窗口->`python setup.py install`
- 运行该脚本还必须先安装py的扩展软件`pywin32-218.win32-py2.7`（根据实际系统选择不同的版本）
