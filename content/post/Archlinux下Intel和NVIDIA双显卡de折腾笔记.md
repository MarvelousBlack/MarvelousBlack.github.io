---
title: "Archlinux 下 Intel 和 NVIDIA 双显卡 de 折腾笔记"
description: "嘛，许多在笔记本安装Arch的小白都会遇到双显卡的问题，经常在群里问。刚刚好我也遇到过于是便有了下文。"
date: 2018-11-26T14:50:56+08:00
topics:
  - 新手教程
tags:
  - Archlinux
  - Nvida
  - Xorg

draft: false
---
嘛，许多在笔记本安装Arch的小白都会遇到双显卡的问题，经常在群里问。刚刚好我也遇到过于是便有了下文。默认你是 ArchLinux 用户，如果是其他发行版，请自行变通。
## 0x00 本教程的主要内容
- 如何在正常的进入 live cd 
- 如何设置并安装私有 NVIDIA 驱动（仅使用n卡渲染然后使用intel输出）
- 设置 Nvidia 相关的视频硬解加速选项
- （追加內容）使用 `bumblebee` 進行切換 **與上面內容使用的方法不同上面提及的顯卡加速設置無效** 請要按照此方法的同學，在閱讀完 0x03 正常的进入live cd 之後，直接拖動滑條，看最下面 0x09 bumblebee 。

## 0x01 开始之前
- 本文针对的是 Nvida 和 intel 双显卡的笔记本，如果是其他奇怪的配置本文可能不适用。
- 这个设置使用了 N 卡渲染，所以只能使用 Nvidia ，在N卡渲染的时候，不能再使用 intel 来进行视频硬解。
- 由于N卡对 wayland 的支持出奇的差，所以这里只使用Xorg。（已经默认你使用并会在之后安装Xorg）
- 本文适用于有硬线连接外屏，但内屏是连接在 intel 显卡上，且 intel显卡无法关闭。或者是你想用 Nvidia 独显来渲染获得更好的性能的同学。
- 对于部分搭载了 **Nvidia MX150** 和 **Nvidia 940MX** 的笔记本，不推荐使用这种方式。因为这个没有预想中的这么好。更加的耗电，而且这两张卡均不支持 NVDEC 视频硬解，还不如使用核显，提升续航和使用 intel 的 vaapi 视频硬解。所以建议使用 `bumblebee` 或者使用 ` nvida-xrun`在必要的时候获得更好的性能。
- 优点是可以获得更好的显示性能和使用和 N 卡直接相连的显示器。缺点是续航大幅度的降低。

## 0x02 我使用的机器
因为本文是以我的经验作为参看，所以下面先列出我的配置，其余的双卡的机器都差不多。 **不是太旧的配置都应该没问题**  

- CPU: Intel i7-7700HQ (8) @ 3.800GHz
- GPU: Intel Device 591b （HD630）
- GPU: NVIDIA GeForce GTX 1050 Ti Mobile

## 0x03 正常的进入live cd
我想很多同学，在安装的时候就已经遇到了问题。使用 `lspci` 之后会发现机器卡死，或者是关机的时候无法关机，更或者在 live cd 加载时候就已经出现了相关的错误提示。这是因为默认 linux 会加载一个叫 **nouveau** 的开源 N 卡驱动这也是出现问题的原因。 你可以 live cd 启动的时候，在选单处按 `e` 然后在启动选项上添加内核参数 `modprobe.blacklist=nouveau` 以暂时禁用导致问题的，nouveau 驱动，然后你就可以继续你的安装了。记得装上 Nvidia 私有驱动哦。 **如果有问题请跳到最下面看补充的内容** >.<

## 0x04 设置并安装私有 NVIDIA驱动
你可能会问，我装好系统之后还需要这样来开机吗？答案是要，在你没有安装 Nvidia 私有驱动之前，你都需要做这一步，当安装完之后就不需要这一步了，因为私有驱动在安装的时候会把这个写入文件里面，自动帮你做了这一步处理，你就不需要再每次开机都添加这一条参数了。  

#### 安装NVIDIA 驱动
这个很简单，只需要使用Arch的~~调教工具~~包管理器，吃豆人。
```bash
sudo pacman -Syu nvidia nvidia-settings xorg-xrandr 
```
如果你需要使用 dkms 请把上面的 `nvidia` 替换为 `nvidia-dkms`。如果使用官方内核装`nvidia`就好了。注：如果是以 root 運行則不需要 sudo。

#### 设置早期KMS启动并启用DRM
这一步你需要做两件事，添加内核参数和 initramfs 模块。
##### 添加内核参数
你需要把 `nvidia-drm.modeset=1` 加入 [kernel parameter](https://wiki.archlinux.org/index.php/Kernel_parameters),下面的例子以 systemd-boot 和 GRUB 为例子
**请不要直接复制，这只是个例子**
###### systemd-boot
修改`/boot/loader/entries/arch.conf`，在`options`后面加入`nvidia-drm.modeset=1`。 例如这样：
```bash
options root=/dev/sda2 quiet splash nvidia-drm.modeset=1
```

###### GRUB
修改 `/etc/default/grub` 找到 `GRUB_CMDLINE_LINUX_DEFAULT`这一项在后面加入 `nvidia-drm.modeset=1`。 例如这样：
```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nvidia-drm.modeset=1”
```
更改完成后执行，下面命令，会重新生成grub.cfg。
```bash
sudo  grub-mkconfig -o /boot/grub/grub.cfg
```
##### 添加 initramfs 模块
在 `/etc/mkinitcpio.conf` 中找到 `MODULES=`这一行在括号的后面加入`nvidia nvidia_modeset nvidia_uvm  nvidia_drm`。加入之后这行看起来是这样的：
```bash
MODULES=(intel_agp i915 nvidia nvidia_modeset nvidia_uvm  nvidia_drm)
```
别忘了重新生成一下 initramfs 执行
```bash
sudo mkinitcpio -P
```
当然，每次更新完 n 卡驱动之后你都需要进行这一步，重新生成 initramfs 如果你觉得麻烦可以使用 pacman hook 。  
创建`/etc/pacman.d/hooks/nvidia.hook`
内容为
```
[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia
Target=linux
# Change the linux part above and in the Exec line if a different kernel is used

[Action]
Description=Update Nvidia module in initcpio
Depends=mkinitcpio
When=PostTransaction
NeedsTargets
Exec=/bin/sh -c 'while read -r trg; do case $trg in linux) exit 0; esac; done; /usr/bin/mkinitcpio -P'
```
这样就可以避免每次更新显卡驱动都要做这一步操作了。 （如果你使用了 `nvidia-dkms` 请把文件中对应项替换）

#### 创建Xorg.conf
先检查有没有这个文件`/usr/share/X11/xorg.conf.d/10-nvidia-drm-outputclass.conf`有的话可以跳过下面的步骤。如果出现找不到显卡可以尝试，加入BUSID指明 n 卡的位置。
新建一个`/etc/X11/xorg.conf.d/10-nvidia-drm-outputclass.conf`  
内容为
```bash
Section "OutputClass"
    Identifier "intel"
    MatchDriver "i915"
    Driver "modesetting"
EndSection

Section "OutputClass"
    Identifier "nvidia"
    MatchDriver "nvidia-drm"
    Driver "nvidia"
    Option "AllowEmptyInitialConfiguration"
    Option "PrimaryGPU" "yes"
    ModulePath "/usr/lib/nvidia/xorg"
    ModulePath "/usr/lib/xorg/modules"
EndSection

```
注意这里的 intel 驱动 **必须为** `modesetting`，否则会产生严重的撕裂，后面的 `AllowEmptyInitialConfiguration` 是为了让 n 卡在没检测到显示器的时候继续运行，而不是默认的退出。

##### 配置桌面管理器
这里以SDDM为例，如果你是其他桌面管理器，请参阅[这里](https://wiki.archlinux.org/index.php/NVIDIA_Optimus#Display_Managers)。如果不配置你将会获得黑屏QwQ。
编辑`/usr/share/sddm/scripts/Xsetup`。添加
```bash
xrandr --setprovideroutputsource modesetting NVIDIA-0
xrandr --auto
```

## 0x05 检查3D
这样你应该就可以正常的使用 N 卡来渲染和 intel 输出了。但还是建议检查一下，安装`mesa-demos`，然后运行
```bash
glxinfo | grep NVIDIA
```
如果看见类似下面的输出，恭喜你成功了。
```bash
[marvelous@Vanilla  ~]$ glxinfo | grep NVIDIA
server glx vendor string: NVIDIA Corporation
client glx vendor string: NVIDIA Corporation
OpenGL vendor string: NVIDIA Corporation
OpenGL core profile version string: 4.6.0 NVIDIA 415.18
OpenGL core profile shading language version string: 4.60 NVIDIA
OpenGL version string: 4.6.0 NVIDIA 415.18
OpenGL shading language version string: 4.60 NVIDIA
OpenGL ES profile version string: OpenGL ES 3.2 NVIDIA 415.18
```

## 0x06 配置视频硬解
惠，等等你是不是忘了什么？哦，对视频硬解。在[Hardware video acceleration](https://wiki.archlinux.org/index.php/Hardware_video_acceleration)词条下面，你可以找到你显卡支持的硬解类型等信息。  
##### 安装并配置
因为用私有驱动，所以你只需要安装 `nvidia-utils`和`libva-vdpau-driver`就可以了。如果你使用`chromium-vaapi`的话建议从aur或者cn源里面安装`libva-vdpau-driver-chromium`，下面假设你启用了cn源，并使用了桌面管理器。
```bash
sudo pacman -Syu nvidia-utils libva-vdpau-driver-chromium  vdpauinfo  libva-utils
```
在`~/.xprofile`中加入
```bash
export LIBVA_DRIVER_NAME=vdpau
export LIBVA_DRIVERS_PATH=/usr/lib/dri/
export VDPAU_DRIVER=nvidia
```
#### 验证 VA-API 工作是否正常
运行 `vainfo` 如果看见类似下面的输出则正常：
```bash
[marvelous@Vanilla  ~]$ vainfo
vainfo: VA-API version: 1.3 (libva 2.3.0)
vainfo: Driver version: Splitted-Desktop Systems VDPAU backend for VA-API - 0.7.4
vainfo: Supported profile and entrypoints
      VAProfileMPEG2Simple            :	VAEntrypointVLD
      VAProfileMPEG2Main              :	VAEntrypointVLD
      VAProfileMPEG4Simple            :	VAEntrypointVLD
      VAProfileMPEG4AdvancedSimple    :	VAEntrypointVLD
      <unknown profile>               :	VAEntrypointVLD
      VAProfileH264Main               :	VAEntrypointVLD
      VAProfileH264High               :	VAEntrypointVLD
      VAProfileVC1Simple              :	VAEntrypointVLD
      VAProfileVC1Main                :	VAEntrypointVLD
      VAProfileVC1Advanced            :	VAEntrypointVLD
```
#### 验证 VDPAU 工作是否正常 
运行`vdpauinfo` 如果看见类似下面的输出则正常：
```bash
[marvelous@Vanilla  ~]$ vdpauinfo
display: :0   screen: 0
API version: 1
Information string: NVIDIA VDPAU Driver Shared Library  415.18  Thu Nov 15 21:34:27 CST 2018

Video surface:

name   width height types
-------------------------------------------
420     8192  8192  NV12 YV12 
422     8192  8192  UYVY YUYV 

Decoder capabilities:

name                        level macbs width height
----------------------------------------------------
MPEG1                           0 65536  4096  4096
MPEG2_SIMPLE                    3 65536  4096  4096
MPEG2_MAIN                      3 65536  4096  4096
H264_BASELINE                  51 65536  4096  4096
H264_MAIN                      51 65536  4096  4096
H264_HIGH                      51 65536  4096  4096
VC1_SIMPLE                      1  8190  2048  2048
VC1_MAIN                        2  8190  2048  2048
VC1_ADVANCED                    4  8190  2048  2048
MPEG4_PART2_SP                  3  8192  2048  2048
MPEG4_PART2_ASP                 5  8192  2048  2048
DIVX4_QMOBILE                   0  8192  2048  2048
DIVX4_MOBILE                    0  8192  2048  2048
DIVX4_HOME_THEATER              0  8192  2048  2048
DIVX4_HD_1080P                  0  8192  2048  2048
DIVX5_QMOBILE                   0  8192  2048  2048
DIVX5_MOBILE                    0  8192  2048  2048
DIVX5_HOME_THEATER              0  8192  2048  2048
DIVX5_HD_1080P                  0  8192  2048  2048
H264_CONSTRAINED_BASELINE      51 65536  4096  4096
H264_EXTENDED                  51 65536  4096  4096
H264_PROGRESSIVE_HIGH          51 65536  4096  4096
H264_CONSTRAINED_HIGH          51 65536  4096  4096
H264_HIGH_444_PREDICTIVE       51 65536  4096  4096
HEVC_MAIN                      153 262144  8192  8192
HEVC_MAIN_10                   --- not supported ---
HEVC_MAIN_STILL                --- not supported ---
HEVC_MAIN_12                   --- not supported ---
HEVC_MAIN_444                  --- not supported ---

Output surface:

name              width height nat types
----------------------------------------------------
B8G8R8A8         32768 32768    y  Y8U8V8A8 V8U8Y8A8 A4I4 I4A4 A8I8 I8A8 
R10G10B10A2      32768 32768    y  Y8U8V8A8 V8U8Y8A8 A4I4 I4A4 A8I8 I8A8 

Bitmap surface:

name              width height
------------------------------
B8G8R8A8         32768 32768
R8G8B8A8         32768 32768
R10G10B10A2      32768 32768
B10G10R10A2      32768 32768
A8               32768 32768

Video mixer:

feature name                    sup
------------------------------------
DEINTERLACE_TEMPORAL             y
DEINTERLACE_TEMPORAL_SPATIAL     y
INVERSE_TELECINE                 y
NOISE_REDUCTION                  y
SHARPNESS                        y
LUMA_KEY                         y
HIGH QUALITY SCALING - L1        y
HIGH QUALITY SCALING - L2        -
HIGH QUALITY SCALING - L3        -
HIGH QUALITY SCALING - L4        -
HIGH QUALITY SCALING - L5        -
HIGH QUALITY SCALING - L6        -
HIGH QUALITY SCALING - L7        -
HIGH QUALITY SCALING - L8        -
HIGH QUALITY SCALING - L9        -

parameter name                  sup      min      max
-----------------------------------------------------
VIDEO_SURFACE_WIDTH              y         1     8192
VIDEO_SURFACE_HEIGHT             y         1     8192
CHROMA_TYPE                      y  
LAYERS                           y         0        4

attribute name                  sup      min      max
-----------------------------------------------------
BACKGROUND_COLOR                 y  
CSC_MATRIX                       y  
NOISE_REDUCTION_LEVEL            y      0.00     1.00
SHARPNESS_LEVEL                  y     -1.00     1.00
LUMA_KEY_MIN_LUMA                y  
LUMA_KEY_MAX_LUMA                y  

```
#### 验证 NVDEC 是否工作正常
使用 `mpv` 这个播放器播放视频，看log输出中有没有 NVDEC 和错误输出。

## 0x07 结语
如果你照着上面的教程，可以正常的使用了，恭喜你。如果不能，很遗憾，请查阅相关资料，或者到 #archlinux-cn-offtopic 中提问。感谢你从头开始阅读本文，如有疏漏，请联系本人更改。

## 0x08 补充内容
在群里问了一下可能还需要一下步骤。于是在这里补充。
在部分机器中可能在做完0x03中提及的步骤后还会卡死，则需要添加如下语句于内核参数中（添加方法在0x03有写）
```file
acpi_osi=! acpi_osi="windows 2009"
```
或者
```
 acpi_os_name="Microsoft Windows NT"
```
再或者
```
 acpi_osi="!Windows2012"
```
如果还有可以继续尝试下面关键字
```
Microsoft Windows NT
"Microsoft Windows XP"
"Microsoft Windows 2000"
"Microsoft Windows 2000.1"
"Microsoft Windows ME: Millennium Edition"
"Windows 2001"
"Windows 2006"
"Windows 2009"
"Windows 2012"
when all that fails, you can even try "Linux"
```
估计就能避免关机还是卡死等问题了。

## 0x09 Bumblebee
引用 Bumblebee FAQ

> Bumblebee 致力于使 NVIDIA Optimus 在 GNU/Linux 系统上可用，实现两块不同的供电配置的显卡同时插入使用，共享同一个 framebuffer。

#### 安裝 Bumblebee
```
sudo pacman -Syu bumblebee nvidia nvidia-settings xorg-xrandr mesa bbswitch 
```
注：如果使用的是 root 用戶運行，則不需要 sudo 。

要使用 Bumblebee 還要添加你的用戶到 `bumblebee` 組,並且啓用 `bumblebeed.service` 。

```
sudo gpasswd -a user bumblebee
systemctl enable bumblebeed.service
```
然後重啓。估計就可以用了。其他關於電源管理 `bbswitch` 和其他雜項 請查閱 [wiki](https://wiki.archlinux.org/index.php/Bumblebee) 和相關文檔。

---
## 参考和引用

- https://wiki.archlinux.org/index.php/NVIDIA_Optimus
- https://wiki.archlinux.org/index.php/NVIDIA
- https://devtalk.nvidia.com/default/topic/957814/linux/prime-and-prime-synchronization/  （这里解释为什么会撕裂和 nvidia 那边是怎么解决的。）
- https://git.archlinux.org/svntogit/packages.git/tree/trunk/nvidia-drm-outputclass.conf?h=packages/nvidia-utils
