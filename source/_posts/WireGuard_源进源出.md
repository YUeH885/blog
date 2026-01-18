---
title: WireGuard 源进源出
date: 2026-01-18 19:20:53
tags:
---
`网络拓扑`
- wan1 198.18.1.2 (默认路由)
- wan2 198.18.2.2 (路由表: 10010)
- WireGuard 监听 2333
- 路由规则 from 192.168.2.2 lookup 10010

理论上，从wan2进来的连接，回复的时候会从wan2发出去，但是WireGuard并不遵循源进源出，它会直接使用默认路由回复，使用源ip 198.18.1.2从wan1发出去，导致握手失败
## 解决方法
在握手包从wan2进来的时候，用ct mark进行标记
```nftables
chain mangle_prerouting {
        type filter hook prerouting priority mangle; policy accept;
        iif "wan2" ct state new ct mark set 0x0000271a
}
```
回复的时候，给这条连接打上fwmark，策略路由走路由表10010出去
```nftables
chain mangle_output {
        type route hook output priority mangle; policy accept;
        ct direction reply meta mark set ct mark
}
```
```shell
ip rule add fwmark 0x271a lookup 10010
```
然后会发现，根本没有用，因为源ip不是进来时的目标ip(198.18.2.2)，连接跟踪会把回复当成新连接，所以不能正确打上fwmark
要用DNAT解决这个问题，握手包进来的时候直接DNAT到198.18.1.2:2333，这样连接跟踪就能正确识别，回复的时候还会自动SNAT成正确的源ip
```nftables
chain dstnat {
        type nat hook prerouting priority dstnat; policy accept;
        iif "wan2" meta nfproto ipv4 udp dport 2333 dnat ip to 198.18.1.2:2333
}
```
至此，源进源出应该能正确工作了
ipv6在我的环境中，不存在上面的问题，它能自动选择正确的源ip回复，原因未知
## 参考
https://jeffz.page/posts/2015670204
https://github.com/openwrt/packages/issues/9538
https://wiki.nftables.org
