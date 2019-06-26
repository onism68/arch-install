# arch安装教程

## 启动盘制作

dd if=xxx.iso of=/dev/sdb

## 联网

#### wifi

wifi-menu

#### 有线

dhcpcd

systemctl enable dhcpcd

systemctl start dhcpcd



#### ping

ping www.baidu.com



## timedate

timedatectl status

timedatectl set-ntp true 

## 修改源

vim /etc/pacman.d/mirrorlist



## 分区

#### lsblk

列出分区

#### parted 创建gpt分区表

parted /dev/sda【安装盘】

mktable

gpt

quit

#### cfdisk

cfdisk /dev/sda 分区

300m efi

剩余 /



## 格式化

#### efi分区

mkfs.vfat /dev/sdb1

#### /分区

mkfs.ext4 /dev/sdb2

## 挂载

mount /dev/sdb2 /mnt

mkdir -p /mnt/boot/EFI

mount /dev/sdb1 /mnt/boot/EFI



## 安装base base-devel

pacstrap /mnt base base-devel

## 生成分区表

genfstab -U /mnt >> /mnt/etc/fstab

## 切换环境

arch-chroot /mnt

## 时区配置

ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

hwclock --systohc

## 本地化

### 安装vim

pacman -S vim

### locale

vim /etc/locale.gen

locale-gen

### 语言

##### vim修改

vim /etc/locale.conf

LANG=en_US.UTF-8

##### 或者

echo LANG=en_US.UTF-8 > /etc/locale.conf

## 给root设置密码

passwd 

## 安装一些必须软件

pacman -S iw wpa_supplicant dialog intel-ucode grub efibootmgr

## grub引导

grub-install --target=x86_64-efi --efi-directory=/boot/EFI --bootloader-id=GRUB

grub-mkconfig -o /boot/grub/grub.cfg



## 添加用户

### 添加

安装zsh

pacman -S zsh

useradd -m -g users -G wheel -s /bin/zsh onism

### sudo提权

visudo

去掉`#%wheel ALL=(ALL) ALL`这一行前面的#



### 设置用户密码

passwd onism

## 退出

exit

## 重启

umount -R /mnt

reboot



## 添加archlinuxcn源

vim /etc/pacman.conf



[archlinuxcn]

SigLevel = Optional TrustedOnly

Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch



sudo pacman -Syu yaourt

sudo pacman -Syu archlinuxcn-keyring



## 桌面环境

### 显卡驱动

sudo pacman -S xf86-video-intel 

### 安装xorg-server

sudo pacman -S xorg-server

### 触摸板驱动

sudo pacman -S xf86-input-synaptics

### alsa

sudo pacman -S alsa-utils

### 中文字体

sudo pacman -S wqy-microhei  ttf-dejavu

### kde桌面

sudo pacman -S plasma

sudo pacman -S sddm

systemctl enable sddm

sddm --example-config > /etc/sddm.conf

##### 修改sddm的相关配置

sudo vim /etc/sddm.conf



## 其他配置

### 网络

sudo pacman -S networkmanager 

systemctl enable NetworkManager

### 联想笔记本WiFi问题

sudo rfkill unblock all

rfkill list all

sudo vim /etc/modprobe.d/ideapad.conf

键入 blacklist ideapad_laptop

保存

重启解决



### 针对NVIDIA双显卡笔记本无法正常关机的问题

1 BIOS禁用独显

2 sudo vim /boot/grub/grub.cfg (升级内核或者更新grub后丢失)

在对应的boot参数后添加 nouveau.modeset=0

3 sudo vim /etc/default/grub 在双引号里面quiet后面加上 nouveau. modeset=0，然后sudo update-grub，这样一旦升级内核或者更新grub，nouveau那句话就不会消失 (此处感谢知乎dearfad)



### fcitx输入法

sudo pacman -S fcitx fcitx-configtool fcitx-gtk2 fcitx-gtk3 fcitx-qt4 fcitx-qt5 fcitx-sogoupinyin

vim .xprofile

export LANG=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8
export XIM="fcitx"
export GTK_IM_MODULE="fcitx"
export QT_IM_MODULE="fcitx"
export XMODIFIERS="@im=fcitx"



### GoLang配置

vim .xprofile

export GOPROXY=https://goproxy.io
export GOROOT=/opt/go
export GOPATH=$HOME/Desktop/Projects/go
export GOBIN=$GOROOT/bin
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH



### 生成初始目录

sudo pacman -S xdg-user-dirs

xdg-user-dirs-update



## 一些软件

### 解压

sudo pacman -S ark

### 图片

sudo pacman -S gwenview

### Chrome

yaourt -S google-chrome

### GIT

sudo pacman -S git

### 代码

sudo pacman -S visual-studio-code-bin

### WPS

sudo pacman -S ttf-wps-fonts wps-office

### Python

#### pip代理

sudo pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pandas

### 代理

sudo pacman -S proxychains-ng

mkdir .proxychains

cp /etc/proxychains.conf ~/.proxychains

