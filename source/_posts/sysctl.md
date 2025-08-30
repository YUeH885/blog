---
title: sysctl
date: 2025-08-28 19:02:15
tags:
---
## bbr+fq
`/etc/sysctl.d/99-bbr.conf`
```
net.ipv4.tcp_congestion_control = bbr
net.core.default_qdisc = fq
```
## ip转发
`/etc/sysctl.d/99-ip_forward.conf`
```
net.ipv4.ip_forward = 1
net.ipv6.conf.all.forwarding = 1
net.ipv6.conf.default.forwarding = 1
net.ipv6.conf.eth0.accept_ra = 2
```
## 关闭反向路径过滤
`/etc/sysctl.d/99-rp_filter.conf`
```
net.ipv4.conf.all.rp_filter = 0
net.ipv4.conf.default.rp_filter = 0
```