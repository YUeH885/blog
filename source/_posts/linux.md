---
title: linux
date: 2025-09-16 13:24:59
tags:
---
## ssh key
### vps
```
echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEpS+PCtOA284BiW9lNjj7iA+cZKHWwCklLyuy4oC+5g yuzu@YUZU-DESKTOP" > .ssh/authorized_keys
```
### local
```
echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFW06bt6jUZcn8YAflV89ni2pK05lP3dQPmqcIdztIV9 yuzu@YUZU-DESKTOP" > .ssh/authorized_keys
```
## 删除残留的配置文件
```
sudo dpkg --purge $(dpkg -l | awk '/^rc/{print $2}')
```
## wireguard 允许所有流量 但不设置默认路由
`/etc/wireguard/wg0.conf`
```
[Interface]
Address = 198.18.0.1/32
Address = 2001:0002::1/128
PrivateKey =
MTU = 1408
Table = 300
PostUp = ip -6 rule add from 2001:0002::1/128 lookup 300
PostUp = ip -6 rule add oif wg0 lookup 300
PostDown = ip -6 rule del from 2001:0002::1/128 lookup 300
PostDown = ip -6 rule del oif wg0 lookup 300

[Peer]
AllowedIPs = 0.0.0.0/0, ::/0
Endpoint =
PublicKey =
PersistentKeepalive = 25
```
## DD debian
```
curl -O https://raw.githubusercontent.com/bin456789/reinstall/main/reinstall.sh
bash reinstall.sh debian
```
### 安装必要的包
```
apt install systemd-timesyncd curl bash-completion vim
```
### 开启自动更新
```
apt install unattended-upgrades apt-listchanges
dpkg-reconfigure unattended-upgrades
```