---
title: archlinux install
date: 2025-09-16 12:13:31
tags:
---
## 分区
```
cfdisk /dev/nvme0n1
```
## 格式化分区
```
mkfs.vfat /dev/nvme0n1px
mkfs.btrfs /dev/nvme0n1px
```
## 挂载
```
mount -o compress=zstd /dev/nvme0n1px /mnt
mount --mkdir /dev/nvme0n1px /mnt/boot
```
## 安装基础系统
```
pacstrap -K /mnt base base-devel linux linux-firmware-intel
```
## 生成fstab
```
genfstab -U /mnt >> /mnt/etc/fstab
```
## chroot到新系统
```
arch-chroot /mnt
```
## 安装必要软件
```
pacman -S networkmanager intel-ucode bash-completion vim
```
## 设置时区
```
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```
## 设置中文
`/etc/locale.gen`
```
en_US.UTF-8
zh_CN.UTF-8
```
```
locale-gen
echo LANG=en_US.UTF-8 >> /etc/locale.conf
```
## 设置hostname
```
echo YUZU-Laptop >> /etc/hostname
```
## 设置root密码
```
passwd
```
## 新建用户
```
useradd -m -G wheel -s /bin/bash yuzu
```
```
passwd yuzu
```
```
EDITOR=vim visudo
```
```
%wheel ALL=(ALL:ALL) ALL
```
## 开启32位仓库
`/etc/pacman.conf`
```
[multilib]
Include = /etc/pacman.d/mirrorlist
```
## 安装systemd-boot
```
bootctl install
```
`/boot/loader/loader.conf`
```
default  arch.conf
timeout  0
console-mode max
editor   no
```
`/boot/loader/entries/arch.conf`
```
title   Arch Linux
linux   /vmlinuz-linux
initrd  /initramfs-linux.img
options root=UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx rw
```
## 安装kde桌面
```
pacman -S plasma-meta sddm konsole dolphin noto-fonts-cjk
systemctl enable sddm NetworkManager
```
## 声卡驱动
```
pacman -S sof-firmware alsa-ucm-conf
```
## 核显驱动
```
pacman -S mesa lib32-mesa intel-media-driver vulkan-intel
```
## 开启蓝牙
```
systemctl enable bluetooth
```
## 完成安装
```
exit
```
```
umount -R /mnt
reboot
```
## 安装paru
```
sudo pacman -S git
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
```
## 中文输入法
```
sudo pacman -S fcitx5-im fcitx5-chinese-addons fcitx5-pinyin-zhwiki
paru -S fcitx5-pinyin-moegirl
```
`/etc/environment`
```
XMODIFIERS=@im=fcitx
```
## 常用软件
```
sudo acman -S ark filelight gwenview spectacle kdeconnect sshfs code
paru -S google-chrome wechat-universal-bwrap linuxqq
```
## chrome硬解
```
--enable-features=VaapiVideoDecoder,VaapiIgnoreDriverChecks,Vulkan,DefaultANGLEVulkan,VulkanFromANGLE
```
## chromium/electron 使用wayland
```
--enable-features=UseOzonePlatform --ozone-platform=wayland --enable-wayland-ime
``` 
## sddm
### 缩放
`/etc/sddm.conf`
```
[General]
GreeterEnvironment=QT_SCREEN_SCALE_FACTORS=2,QT_FONT_DPI=192
```
### 中文
```
sudo systemctl edit sddm.service
```
```
[Service]
Environment=LANG=zh_CN.UTF-8
```