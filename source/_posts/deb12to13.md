---
title: debian12升级到debian13
date: 2025-09-16 13:53:22
tags:
---
## 升级软件包
```
apt update -y
apt dist-upgrade -y
```
## 修改软件源到 trixie
```
sed -i 's/bookworm/trixie/g' /etc/apt/sources.list /etc/apt/sources.list.d/*.list /etc/apt/sources.list.d/*.sources
```
## 最小升级
```
apt update -y
apt upgrade --without-new-pkgs -y
```
## 完整升级
```
apt full-upgrade -y
```
## 重启
```
reboot
```
## 清理不需要的软件包和配置文件
```
apt autoremove --purge
apt list '~o'
apt purge '~o'
apt list '~c'
apt purge '~c'
```
## 更新软件源到新的格式
```
apt modernize-sources
rm /etc/apt/sources.list.bak
rm /etc/apt/sources.list.d/*.list.bak
```