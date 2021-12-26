arch的安装，备份与迁移

1安装
1、联网
2、调整时间
	timedatectl set-ntp true
	set-zone
3、硬盘分区及格式化
	fdisk -l
	mkfs.fat -F32 /dev/sda1
	mkfs.ext4 /dev/sda2
4、挂载分区
使用 mount /dev/sda2 /mnt 将 /dev/sda2 分区挂载到 /mnt
使用 mkdir /mnt/boot 在 /dev/sda2 分区新建 boot 目录
使用 mount /dev/sda1 /mnt/boot 将 /dev/sda1 分区挂载到 /mnt/boot
5、安装
/etc/pacman.d/mirrorlist，在其前两行加入

Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
pacstrap /mnt base base-devel linux linux-firmware dhcpcd vim
6、生成fstab配置
genfstab -U /mnt >> /mnt/etc/fstab
7、切换根目录
arch-chroot /mnt
8、时区配置
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
9、本地化
在 /etc/locale.gen 中取消 en_US.UTF-8 UTF-8 和 zh_CN.UTF-8 UTF-8 的注释
之后运行 locale-gen

由于还没有安装中文字体，为了避免出现豆腐块，先设置语言为英文
新建 /etc/locale.conf 文件，写入 LANG=en_US.UTF-8
10、网络配置
新建 /etc/hostname 文件，写入主机名

假设主机名为 myhostname
编辑 /etc/hosts 文件，写入

127.0.0.1 localhost
::1       localhost
127.0.1.1 myhostname.localdomain myhostname
11、修改密码
12、配置引导
因为使用 EFI 模式启动，除了 GRUB 本体之外还要安装 efibootmgr

pacman -Syu grub efibootmgr
将 GRUB 安装到引导分区

grub-install --efi-directory=/boot
配置引导程序
GRUB 可以自动生成配置文件

grub-mkconfig -o /boot/grub/grub.cfg
13、重启
exit
umount -R /mnt
14、重启后联网
systemctl enable --now dhcpcd
ln -s /usr/bin/vim /usr/bin/vi
useradd -m -G wheel 用户名
使用 visudo 来编辑 sudo 的配置，避免因为配置问题导致 sudo 彻底不可用

找到 root ALL=(ALL) ALL，在其下一行加入 用户名 ALL=(ALL) ALL 并保存

之后可以 exit 后使用新用户登录




##
2、备份
备份有多种策略
1、tar
2、rsync
3\ dd
cd /
sudo tar cvpzf backup.tgz --exclude=/proc --exclude=/mnt --exclude=/sys --exclude=/backup.tgz /
3、恢复
1、使用liveCD 进入新系统 分好区，挂载完成。
2、把备份包解压至挂载分区并创建部分系统目录mkdir proc sys mnt media
3、更新fstab
genfstab -U /mnt >> /mnt/etc/fstab
在/mnt/etc/fstab的末尾插入任意注释，如#end of old fstab。按照安装指南#Fstab中的说明生成一个新的fstab文件，并将其附加到当前的fstab文件中。一定要检查genfstab创建的fstab文件。在这种情况下，请在注释之前检查较旧的fstab条目，删除过期项和重复项；如果旧条目仍然有用，则保留它们。例如，可以保留网络驱动器的挂载条目。一般推荐使用持久化命名。
4、重新生成引导
arch-chroot
pacman -Syu grub efibootmgr
grub-install --efi-directory=/boot
grub-mkconfig -o /boot/grub/grub.cfg
5、重启