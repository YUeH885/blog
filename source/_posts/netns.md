---
title: netns
date: 2025-08-28 18:46:22
tags:
---

## 创建ns
```
ip netns add example
```
## 创建veth
```
ip link add example-host type veth peer name example-ns
```
## 把veth移到ns内
```
ip link set example-ns netns example
```
## 添加ip
```
ip addr add 198.18.0.1/24 dev example-host
ip addr add 2001:0002::1/64 dev example-host
ip netns exec example ip addr add 198.18.0.2/24 dev example-ns
ip netns exec example ip addr add 2001:0002::2/64 dev example-ns
```
## 启动接口
```
ip link set example-host up
ip netns exec example ip link set example-ns up
ip netns exec example ip link set lo up
```
## 添加路由
```
ip netns exec example ip route add default via 198.18.0.1
ip netns exec example ip route add default via 2001:0002::1
```
## 设置ns内的dns
`/etc/netns/example/resolv.conf`
```
nameserver 1.1.1.1
```
然后配置[snat](/nftables/#snat)和[ip转发](/sysctl/#ip转发)
## 把wireguard放到ns内
`/etc/wireguard/wg0.conf`
```
[Interface]
PrivateKey = 
ListenPort = 19190
PostUp = /usr/sbin/ip netns add wireguard
PostUp = /usr/sbin/ip link set %i netns wireguard
PostUp = /usr/sbin/ip netns exec wireguard ip addr add dev %i 198.18.0.1/24
PostUp = /usr/sbin/ip netns exec wireguard ip addr add dev %i 2001:0002::1/64
PostUp = /usr/sbin/ip netns exec wireguard ip link set %i up
PostUp = /usr/sbin/ip netns exec wireguard ip link set lo up
[Peer]
PublicKey = 
AllowedIPs = 
```
> WireGuard 做了一些非常有趣的事情。当创建一个 WireGuard 接口时（使用 ip link add wg0 type wireguard），它会记住它被创建时所在的命名空间。“我是在命名空间 A 中创建的。” 之后，WireGuard 可以被移动到新的命名空间（“我正在移动到命名空间 B。”），但它仍然会记住它起源于命名空间 A。
WireGuard使用一个UDP套接字来实际发送和接收加密数据包。这个套接字始终存在于命名空间A——原始出生地命名空间。这带来了一些非常酷的特性。也就是说，你可以在一个命名空间（A）中创建WireGuard接口，将其移动到另一个命名空间（B），并且使从命名空间B发送的明文数据包通过命名空间A中的UDP套接字进行加密发送。
[链接](https://www.wireguard.com/netns/)
## systemd管理的服务放到ns内
`service.d/override.conf`
```
[Service]
NetworkNamespacePath=/var/run/netns/example
```