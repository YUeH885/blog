---
title: WireGuard 允许所有流量 但不设置默认路由
date: 2026-01-26 21:54:25
tags:
---
`/etc/wireguard/wg0.conf`
```
[Interface]
Address = 198.18.0.1/32
Address = 2001:0002::1/128
PrivateKey =
MTU = 1408
Table = 300
PostUp = ip rule add from 198.18.0.1/32 lookup 300
PostUp = ip -6 rule add from 2001:0002::1/128 lookup 300
PostUp = ip rule add oif wg0 lookup 300
PostUp = ip -6 rule add oif wg0 lookup 300
PostDown = ip rule del from 198.18.0.1/32 lookup 300
PostDown = ip -6 rule del from 2001:0002::1/128 lookup 300
PostDown = ip rule del oif wg0 lookup 300
PostDown = ip -6 rule del oif wg0 lookup 300

[Peer]
AllowedIPs = 0.0.0.0/0, ::/0
Endpoint =
PublicKey =
PersistentKeepalive = 25
```
