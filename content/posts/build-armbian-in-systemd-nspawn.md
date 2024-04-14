---
title: "在 systemd-nspawn 容器下构建 Armbian 镜像文件"
date: 2024-04-13T20:53:52+08:00
description: "通常构建 Armbian 是使用 Armbian Linux Build Framework ，但是这个项目要求使用 Debian 或者 Ubuntu 来运行。最简单的方案自然是使用对应的系统或者安装虚拟机，但现在我使用的设备运行的是 Arch Linux 且不希望使用虚拟机。因此通过使用 systemd-nspawn 容器来运行 Ubuntu(jammy) 来构建刷入开发板的镜像文件。在这个过程中遇到的问题也在这记录一下。"
draft: false
topics:
  - 杂乱的笔记
tags:
  - armbian
  - systemd-nspawn
---
简单的来说就是创建个 systemd-nspawn 的容器，然后跑就完事了。但问题远远没有这么简单（x

## 0x00 前言/需求
最近买了个小玩具，这个开发板呢没有 Armbian 官方的预构建支持（就是不能直接下到 Armbian 构建好的镜像），只能手动用 Armbian Linux Build Framework 来构建需要的开发板镜像文件。问题来了，正如预览所写的，使用这个构建工具需要使用 Debian 或 Ubuntu ，但我现在使用的系统是 Arch Linux ，没有办法直接运行这个东西。当然可以用他们提供的其中一个方法使用 docker ，但这样也太不优雅了。还不如直接起个 systemd-nspawn 容器来的方便，所有就有了这个记录。

## 0x01 准备工作
既然要用 systemd-nspawn 了，那我们的宿主当然需要一个使用 systemd ~~~把底裤卖掉~~~ 的发行版，好要准备一个 Debian 或者 Ubuntu 的 rootfs。当然直接 debootstrap 一个就行。  
注意：
>  systemd-nspawn 要求容器中的作業系統以 PID 1 運行systemd init，並且 systemd-nspawn 要安裝在容器中。為了完善依賴，需確保在容器中安裝 debian 軟件包 systemd-container，debian 包 systemd-container 依賴 dbus，但是 dbus 不會被默認安裝。所以還要確保 dbus 或 dbus-broker 這些 debian 包被安裝在容器內系統中。   

直接 debootstrap 一个 Ubuntu(jammy) 这里容器的名称为 ubuntu：
```
# debootstrap --include=dbus-broker,systemd-container --components=main,universe jammy ubuntu "http://archive.ubuntu.com/ubuntu/"
```
在创建好之后，需要给 root 用户一个密码，因为 Debian 和 Ubuntu 不會在首次登錄時無密碼登錄。  
创建 root 密码
```
# systemd-nspawn -D ubuntu
# passwd
# logout
```
这样就创建好 root 用户的密码了。  
接着启动容器  
```
# systemd-nspawn -b -D ubuntu
```
在一段滚屏过后，我们就看见下面这样的东西。（Yume 是我的机器名）
```
Ubuntu 22.04 LTS Yume console

Yume login: 
Password:
```
接着用 root 用户登陆进去，继续安装需要的软件以及配置环境。需要安装 git 和 sudo 并创建一个普通用户并且赋予该用户 sudo 的权限。

```
# apt update
# apt install git sudo
# adduser ubuntu
# usermod -aG sudo ubuntu
# logout
```
至此需要的容器已经准备好了！

## 0x02 构建镜像

切换至刚刚新建的 ubuntu 用户，按照[文档](https://docs.armbian.com/Developer-Guide_Build-Preparation/)的指引开始启动并构建对应的镜像文件。因为已经是用容器了，没必要再用 docker 了，因此加上`PREFER_DOCKER=no` 。
```
$ git clone --depth=1 --branch=main https://github.com/armbian/build  
$ cd build  
$ ./compile.sh PREFER_DOCKER=no 
```
对就这么简单！

## 0x03 遇到问题
但很快就遇到提示找不到 loop 设备
```
[🔨]   The partition table has been altered.
[🔨]   Syncing disks.
find: ‘/dev/loop*’: No such file or directory
Usage: grep [OPTION]... PATTERNS [FILE]...
Try 'grep --help' for more information.
```
和 `check_loop_device_internal` 命令执行失败的提示
```
[🚸] Command failed, retrying in 5s [ check_loop_device_internal  ]
[🚸] Command failed 5 times, giving up [ check_loop_device_internal  ]
[💥] error! [ Device node  does not exist after 5 tries.  ]
[💥] Cleaning up [ please wait for cleanups to finish ]
```
这个问题是由于在容器里面没有足够的权限创建 loop 设备。放狗搜索了一下，找到一个相关的，[Building Armbian using Ubuntu (jammy) in a systemd-nspawn container](https://gist.github.com/ag88/05245121cce37cb8f029424a20752e35)[1]，思路是给容器权限去创建设备，并且配合修改构建脚本。  
在宿主设备上重新用下面命令运行 systemd-nspawn 容器,给容器 MKNOD 权限。
```
# systemd-nspawn -b --capability=CAP_MKNOD \
        --property=DeviceAllow="block-loop rwm" \
        --property=DeviceAllow="block-blkext rwm" \
        --property=DeviceAllow="/dev/loop-control rwm" \
        -D /opt/armbian-build 
```
给了权限还不够还需要接着给构建脚本打 patch 让构建脚本可以创建 loop 设备，因为上面的链接[1]时间有点久了，构建脚本和以前的有部分修改已经对不上了因此需要对 patch 修改。  
下面是修改好的[patch](/public/file/build.patch)的内容
```
diff --git a/lib/functions/image/loop.sh b/lib/functions/image/loop.sh
index 68a2f02..df08e09 100644
--- a/lib/functions/image/loop.sh
+++ b/lib/functions/image/loop.sh
@@ -115,3 +115,63 @@ function free_loop_device_retried() {
 	fi
 	losetup -d "${1}"
 }
+
+
+function create_loop_devices() {
+	if ! test -e /dev/loop-control; then
+	  display_alert "creating loop control device" "/dev/loop-control"
+	  run_host_command_logged mknod /dev/loop-control c 10 237
+	fi
+	
+	local i
+	for i in 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15; do
+	if ! test -e /dev/loop${i}; then
+	  display_alert "creating loop device" "/dev/loop${i}"
+	  run_host_command_logged mknod /dev/loop${i} b 7 ${i}
+	fi
+	done
+}
+
+function dolosetup(){
+	local loopdev=$1
+	local image_file=$2
+	if test -z "${image_file}"; then
+	  exit_with_error "image file not specified"
+	fi
+	if ! test -e $image_file; then
+	  exit_with_error "image file ${image_file} not found"
+	fi
+	
+	if test -z "${loopdev}"; then
+	  exit_with_error "loop device not specified"
+	fi
+	display_alert "loop device" "${loopdev}" "debug"
+	if ! test -e ${loopdev}; then
+	  exit_with_error "image file ${loopdev} not found"
+	fi
+
+	run_host_command_logged losetup -P ${loopdev} ${image_file}
+	#check that loop device is linked
+	local lochk=$(losetup -j ${image_file} |sed 's/: .*$//')
+	if test "${lochk}" != "${loopdev}" ; then
+	  exit_with_error "losetup unable to setup ${loopdev}"
+	fi
+	
+	# drop the first line, as this is our loopdev itself, but we only want the child partitions
+	local partitions=$(lsblk --raw --output "MAJ:MIN" --noheadings ${loopdev} | tail -n +2)
+	if echo ${partitions} | egrep -q "[[:digit:]]:[[:digit:]].*" ; then
+	  local counter=1
+	  for i in $partitions; do
+	    local maj=$(echo $i | cut -d: -f1)
+	    local min=$(echo $i | cut -d: -f2)
+	    display_alert "partition" "${loopdev}p${counter} b ${maj} ${min}" "debug"
+	    if [ ! -e "${loopdev}p${counter}" ]; then
+	      display_alert "create partition loop device" "${loopdev}p${counter} b ${maj} ${min}"
+	      mknod ${loopdev}p${counter} b ${maj} ${min}
+	    fi
+	    counter=$((counter + 1))
+	  done
+	else
+	  exit_with_error "cannot get MAJ:MIN ${maj}:${min} from lsblk"
+	fi
+}
diff --git a/lib/functions/image/partitioning.sh b/lib/functions/image/partitioning.sh
index cd614eb..32b9825 100644
--- a/lib/functions/image/partitioning.sh
+++ b/lib/functions/image/partitioning.sh
@@ -215,6 +215,7 @@ function prepare_partitions() {
 	exec {FD}> /var/lock/armbian-debootstrap-losetup
 	flock -x $FD

+	create_loop_devices
 	declare -g LOOP
 	# replace losetup --find with own function and do 10 cycles
 	# losetup always return 1st free loop device and in parallel build,
@@ -234,7 +235,8 @@ function prepare_partitions() {
 	CHECK_LOOP_FOR_SIZE="no" check_loop_device "$LOOP" # initially loop is zero sized, ignore it.

         #--partscan is using to force the kernel for scaning partition table in preventing of partprobe errors
-	run_host_command_logged losetup --partscan "${LOOP}" "${SDCARD}".raw
+	#run_host_command_logged losetup --partscan "${LOOP}" "${SDCARD}".raw
+	dolosetup "${LOOP}" "${SDCARD}".raw

 	# loop device was grabbed here, unlock
 	flock -u $FD
```
把 patch 放到 build 目录下面，然后再尝试构建问题解决。
```
$ patch -b -p1  < build.patch
$ ./compile.sh PREFER_DOCKER=no
```
## 0x03 结尾
整个过程下来也没多少东西，这里算是做个记录吧。很简单起个容器，运行脚本，然后因为容器权限的问题，需要对构建脚本打个小小的补丁。这里也要感谢 [@FQEGG](https://t.me/FQEGG) 的帮助，因为之前确实没怎么用过`systemd-nspawn`，在这个过程中也给我提供了不少的帮助，顺便帮忙测试了这个方法的可行性。  
嘛，都用上 systemd-nspawn 了，这里再安利点好玩的吧 -> [别惦记你那All In BOOM了，来用容器跑软路由吧](https://kirikira.moe/post/49/)。

---

## 參考資料以及鏈接
[1] https://gist.github.com/ag88/05245121cce37cb8f029424a20752e35  
[2] https://docs.armbian.com/Developer-Guide_Build-Preparation/  
[3] https://wiki.archlinux.org/title/Systemd-nspawn  
[🔨] https://kirikira.moe/post/49/

