---
title: 使用 RDP 连接 Hyper-V
date: 2025-08-27 15:43:39
tags:
---
## 授予连接权限
```
Grant-VMConnectAccess -VMName <虚拟机名称> -UserName <用户名>
```
## 撤销连接权限
```
Revoke-VMConnectAccess -VMName <虚拟机名称> -UserName <用户名>
```
## 获取VMId
```
Get-VM -Name <虚拟机名称> | Select-Object -ExpandProperty VMId
```
## 使用RDP连接
`VM.rdp`
```
pcb:s:<获取到的VMId>;EnhancedMode=0
full address:s:<宿主IP>
server port:i:2179
username:s:<宿主用户名>
negotiate security layer:i:0
```
## 使用FreeRDP连接
```
wlfreerdp3 /u:<宿主用户名> /vmconnect:<获取到的VMId> /v:<宿主IP>
```