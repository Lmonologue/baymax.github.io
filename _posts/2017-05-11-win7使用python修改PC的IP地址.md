---
layout:     post
title:      "win7使用python修改PC的IP地址"
subtitle:   "修改IP、ping"
date:       2017-05-11 22:00:00
author:     "HJ"
header-img: "img/post-bg-2017.jpg"
catalog: true
tags:
    - python
---

### 前言

由于实验室IP可以上网的IP地址很少，也容易被他人抢占，为了找到可以用的IP，结合网上资源凑了一个小程序，可以在一定范围内修改PC的IP，然后去ping网络

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
