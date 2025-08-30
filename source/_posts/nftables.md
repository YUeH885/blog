---
title: nftables
date: 2025-08-27 08:34:19
tags:
---
## snat
```
table inet main {
    chain postrouting {
        type nat hook postrouting priority srcnat;
        oifname "eth0" ip saddr 192.168.1.1/24 snat ip to 198.18.0.1
        oifname "eth0" ip6 saddr fd00::1/64 snat ip6 to 2001:0002::1
    }
}
```
## dnat
```
table inet main {
    chain prerouting {
        type nat hook prerouting priority dstnat;
        iifname "eth0" ip daddr 198.18.0.1 tcp dport 19190 dnat to 192.168.1.1:19190
        iifname "eth0" ip6 daddr 2001:0002::1 udp dport 19190 dnat to [fd00::1]:19190
    }
}
```
## mss clamp
```
table inet main {
    chain mssclamp {
        type filter hook postrouting priority 0; policy accept;
        oifname "wg0" tcp flags syn tcp option maxseg size set rt mtu
    }
}
```
## dscp mark
```
table inet main {
    chain output {
        type route hook output priority mangle; policy accept;
        oifname "eth0" meta skuid 1001 ip dscp set cs1
        oifname "eth0" meta skuid 1001 ip6 dscp set cs1
        oifname "eth0" meta mark 0x00000039 ip dscp set cs1
        oifname "eth0" meta mark 0x00000039 ip6 dscp set cs1
    }
}
```
## flowtable
```
table inet main {
    flowtable ft {
        hook ingress priority filter
        devices = { eth0 }
    }

    chain forward {
        type filter hook forward priority filter;
        ct state established flow add @ft
    }
}
```