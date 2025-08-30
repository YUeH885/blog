---
title: windows
date: 2025-09-16 13:16:24
tags:
---
## 跳过联网
```
OOBE\BYPASSNRO
```
## LTSC安装应用商店
```
wsreset -i
```
## 安装adbtools
```
winget install Google.PlatformTools
```
## 释放保留空间
```
DISM.exe /Online /Set-ReservedStorageState /State:Disabled
```
## 卸载小组件
```
winget uninstall “windows web experience pack”
```
## PowerShell
### ssh host tab补全
`C:\Users\yuzu\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1`
```
Register-ArgumentCompleter -CommandName ssh,scp,sftp -Native -ScriptBlock {
		param($wordToComplete, $commandAst, $cursorPosition)
		$lstHosts = Get-Content ~\.ssh\config|%{ if ($_.trim() -match "^Host\s[\w|_|\d|-]*$") {$_.ToString().Trim().split(" ")[1]}}
		foreach ($val in $lstHosts) {
			if ($val -like "$wordToComplete*") {
				$val
			}
		}
	}
```
### 允许执行脚本
```
Set-ExecutionPolicy RemoteSigned
```
## QoS
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