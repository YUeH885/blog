---
title: pve
date: 2025-09-16 09:21:32
tags:
---
## 导入镜像
```
qm importdisk <vmid> <Image> <Storage> --format <qcow2 | raw | vmdk>
```
## 修改硬盘大小
```
qm resize <vmid> <scsi0> <1G>
```