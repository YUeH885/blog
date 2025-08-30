---
title: Windows QoS
date: 2025-09-16 13:16:24
tags:
---
**要使用pwsh创建，在组策略创建TCP不生效**
### 添加规则
```
New-NetQosPolicy -Name "<Chrome>" `
  -AppPathNameMatchCondition "<chrome.exe>" `
  -DSCPAction <8> `
  -NetworkProfile All `
  -IPProtocolMatchCondition Both
```
### 查看规则
```
Get-NetQosPolicy -Name "<Chrome>" | Format-List *
```
### 删除规则
```
Remove-NetQosPolicy -Name "<Chrome>"
```
