---
title: Linux分区(Parted)
date: 2018-03-22 09:31:46
tags:
    - Linux
    - 系统安装
---
## 环境检查

```
ls /sys/firmware/efi/efivars #UEFI/BIOS检测
```
若该目录不存在，则 ArchISO 是以 BIOS/CSM 模式启动，否则是以 UEFI 模式启动。

> 通常而言，UEFI 系统须使用 GPT 分区才能引导，BIOS 系统须使用 MBR 分区才能引导。

## 分区

#### fdisk
```bash
fdisk -l //查看所有分区情况
```
*常用fdisk命令：p 显示当前磁盘分区，d 删除指定分区，n 创建新分区， a 为指定分区创建启动标记，t 更改分区格式， w将磁盘分区信息写入磁盘。*

#### parted

```
# parted /dev/sda

检查 MINOR #对文件系统进行一个简单的检查
cp [FROM-DEVICE] FROM-MINOR TO-MINOR #将文件系统复制到另一个分区
help [COMMAND] #打印通用求助信息，或关于 COMMAND 的信息
mklabel 标签类型 #创建新的磁盘标签 (分区表)
mkfs MINOR 文件系统类型 #在 MINOR 创建类型为“文件系统类型”的文件系统
mkpart 分区类型 [文件系统类型] 起始点 终止点 #创建一个分区
mkpartfs 分区类型 文件系统类型 起始点 终止点 #创建一个带有文件系统的分区
move MINOR 起始点 终止点 #移动编号为 MINOR 的分区
name MINOR 名称 #将编号为 MINOR 的分区命名为“名称”
print [MINOR] #打印分区表，或者分区
quit #退出程序
rescue 起始点 终止点 #挽救临近“起始点”、“终止点”的遗失的分区
resize MINOR 起始点 终止点 #改变位于编号为 MINOR 的分区中文件系统的大小
rm MINOR #删除编号为 MINOR 的分区
select 设备 #选择要编辑的设备
set MINOR 标志 状态 #改变编号为 MINOR 的分区的标志

# mkfs.vfat -F32 /dev/sda1 #生成ESP分区的文件系统FAT32
```

#### 格式化分区
```
mkfs.ext4 /dev/sda1 #格式化ext4
mkswap /dev/sda5 #格式化swap
```

#### 分区方案
```
/dev/sda1 /boot 500M
/dev/sda2 扩展分区 剩余所有空间
/dev/sda5 swap 8G(根据内存大小调整)
/dev/sda6 / 20G
/dev/sda7 /home 剩余所有空间
```

#### 挂载分区
```
#mount /dev/sda6 /mnt #挂载根分区
```
非UEFI挂载boot
```
# mkdir -p /mnt/boot
# mount /dev/sda1 /mnt/boot
```

建立efi目录，把EFI分区装载到刚建立的efi目录上。

```
#mkdir -p /mnt/boot/efi
#mount /dev/sdc1 /mnt/boot/efi
```

挂载交换分区和home
```
#swap on /dev/sda5
#mkdir -p /mnt/home
#mount /dev/sda7 /mnt/home
```

#### 生成引导
* BIOS：

> 依赖包： grub os-prober

```
# grub-install --recheck /dev/<目标磁盘>
# grub-mkconfig -o /boot/grub/grub.cfg
```

* UEFI：---如果BIOS是UEFI的，就要用下面的命令安装grub了

> 依赖包： dosfstools grub efibootmgr

```
# grub-install --target=x86_64-efi --efi-directory=<EFI 分区挂载点> --bootloader-id=arch_grub --recheck
# grub-mkconfig -o /boot/grub/grub.cfg
```

**低格填零**
```
# dd if=/dev/zero of=/dev/sda bs=16M
```