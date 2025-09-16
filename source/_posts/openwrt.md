---
title: openwrt
date: 2025-09-16 08:57:07
tags:
---
## 自定义设备 dns/网关
`/etc/config/dhcp`
```
config tag '<tag>'
	    option dhcp_option '<6,1.1.1.1 3,192.168.1.2>'
```
然后在wubui dhcp/dns 静态地址分配 添加标签
或者
`/etc/config/dhcp`
```
config host
        option name '<hostname>'
        option ip '<192.168.1.11>'
        list mac '<mac>'
        list tag '<tag>'
```
## NPTv6
`/etc/config/firewall`
```
config include
        option enabled 1
        option type 'script'
        option path '/etc/nftables.d/NPTv6.sh'
        option fw4_compatible 1
```
`/etc/nftables.d/NPTv6.sh`
```
#!/bin/ash
#LAN_PFX="$(uci -q get network.globals.ula_prefix)"
LAN_PFX="<2001:0002::/64>"
PD_PREFIX=$(ifstatus wan_6 | jq -r '.["ipv6-prefix"][] | "\(.address)/\(.mask)"')
nft add rule inet fw4 srcnat oifname "pppoe-wan" snat ip6 prefix to ip6 saddr map { "${LAN_PFX}" : "${PD_PREFIX}" }
nft add rule inet fw4 dstnat iifname "pppoe-wan" dnat ip6 prefix to ip6 daddr map { "${PD_PREFIX}" : "${LAN_PFX}" }
```
## 清空连接跟踪
```
echo > /proc/net/nf_conntrack
```
## 自用Image Builder
```
make image \
ROOTFS_PARTSIZE="1004" \
PACKAGES="-kmod-amazon-ena -kmod-amd-xgbe -kmod-bnx2 -kmod-dwmac-intel -kmod-e1000e -kmod-e1000 -kmod-forcedeth -kmod-fs-vfat -kmod-igb -kmod-ixgbe -kmod-r8169 -kmod-tg3 -kmod-drm-i915 \
kmod-igc kmod-nft-tproxy \
luci luci-i18n-base-zh-cn luci-i18n-package-manager-zh-cn luci-i18n-firewall-zh-cn luci-i18n-sqm-zh-cn ddns-scripts-cloudflare luci-i18n-ddns-zh-cn openssh-sftp-server tcpdump jq python3 luci-proto-wireguard"
```