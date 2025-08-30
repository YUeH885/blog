---
title: netns
date: 2025-08-28 18:46:22
tags:
---

## 创建ns
```
ip netns add <ns>
```
## 创建veth
```
ip link add <veth-host> type veth peer name <veth-ns>
```
## 把veth移到ns内
```
ip link set <veth-ns> netns <ns>
```
## 添加ip
```
ip addr add <198.18.0.1/24> dev <veth-host>
ip addr add <2001:0002::1/64> dev <veth-host>
ip -n <ns> addr add <198.18.0.2/24> dev <veth-ns>
ip -n <ns> addr add <2001:0002::2/64> dev <veth-ns>
```
## 启动接口
```
ip link set <veth-host> up
ip -n <ns> link set <veth-ns> up
ip -n <ns> link set lo up
```
## 添加路由
```
ip -n <ns> route add default via <198.18.0.1>
ip -n <ns> route add default via <2001:0002::1>
```
## 设置ns内的dns
`/etc/netns/<ns>/resolv.conf`
```
nameserver 1.1.1.1
```
## 把wireguard放到ns内
```
ip netns add <wireguard>
ip link add dev <wg0> type wireguard
wg setconf <wg0> /etc/wireguard/wg0.conf
ip link set <wg0> netns <wireguard>
ip -n <wireguard> link set lo up
ip -n <wireguard> link set <wg0> up
ip -n <wireguard> addr add dev <wg0> 198.18.0.1/24
ip -n <wireguard> addr add dev <wg0> 2001:0002::1/64
```
> WireGuard 做了一些非常有趣的事情。当创建一个 WireGuard 接口时（使用 ip link add wg0 type wireguard），它会记住它被创建时所在的命名空间。“我是在命名空间 A 中创建的。” 之后，WireGuard 可以被移动到新的命名空间（“我正在移动到命名空间 B。”），但它仍然会记住它起源于命名空间 A。
WireGuard使用一个UDP套接字来实际发送和接收加密数据包。这个套接字始终存在于命名空间A——原始出生地命名空间。这带来了一些非常酷的特性。也就是说，你可以在一个命名空间（A）中创建WireGuard接口，将其移动到另一个命名空间（B），并且使从命名空间B发送的明文数据包通过命名空间A中的UDP套接字进行加密发送。
[链接](https://www.wireguard.com/netns/)
## systemd管理的服务放到ns内
`service.d/override.conf`
```
[Service]
NetworkNamespacePath=/var/run/netns/<ns>
```
