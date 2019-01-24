---
title: "寨板折腾笔记"
date: 2019-01-17T22:16:42+08:00
description: "嘛，好久没更新了，今天就来水一篇。顺便把上次说的坑填一下。"
topics:
  - 琐碎的事
  - 杂乱的笔记
tags:
  - archlinux 
  - tablet
  - usb chager
draft:  false
---
emmm， 上次我提到了要给收回来的那块寨版 X80PLUS 装上 ArchLinux ，但是过了好多天都没写，其实我早在几天之前就装好了，但是发生了一些小插曲，于是就拖到了现在。那现在就开始聊一聊发生了什么吧。

# 0x00 概况
用 Intel cpu 的寨板基本上是可以安装 ArchLinux 的，但是不是所有的元件都能 work 的，这个要看运气。嘛，我只有一块板，下面先列出我手上这块xx80plus板子安装好之后各个能工作的原件吧。这个平板是个 z8300+2G+32G的常见配置。如果是其他的板子的话，我也不清楚是不是可以用，大家可以先 google 一下。

![pic](/public/pic/things-about-x80plus-neofetch.png)

#### X80PLUS(H5C5) 
- 显示：working（xf86-video-intel）
- 触屏：working (goodix)
- WiFi：working（存在一定问题）(rt8723bs)
- 蓝牙：working (rt8723bs)
- 声卡：working (ALSA)
- 电源状态：working
- 充电：working
- OTG：working
- SD卡读卡器：Unknown(我手上没卡所以没有测试)
- 加速的传感器：working(需要修改hwdb文件)
- 音量按钮：Not working （能检测的到）
- 屏幕背光调节：working 
- 摄像头：Not working（一个叫 ov2680 的东西，没驱动）

大部分都是开箱即用，不需要什么设置，也就是坑已经被前人踩完了。

# 0x01 安装 ArchLinux 和设置
嘛，如何安装 archlinux 这个问题，萌狼已经写过了，所以我在这里就只写和这个板子有关和一些剩下的一些问题。汝如果想看如何安装请看[这里](https://blog.yoitsu.moe/arch-linux/installing_arch_linux_for_complete_newbies.html),咱可是照着这个做了一遍十分简单易懂。值得注意的是这个平板只有一个 usb 口的说，汝只能选择只充电，或者是只 OTG ，所以要先充满电然后再开始哦。  
#### 网络
咱在上面提到了一件事就是 wifi 是存在问题的，这个 wifi 模块的名字是 rt8723bs 驱动已经包含在了新的 linux 内核中了，但是，我已经用了最新的稳定内核了，但还是遇到了玄学问题。表现的现象是这样的，能够扫描到 wifi 但是连接的话会被拒绝。也就是能扫不能连，这个故障比较玄学换了路由器又可以了。而且我还查看了路由器和平板的log均没有收获遂放弃。但由于我当时没有其他的路由器和网卡，于是只能把手机连上去，用usb共享功能，来完成安装。安装完之后去淘宝买了个usb有线网卡，才想起来咱手上有个 wifi 放大器可以试试，可以连，于是就这样将就着用了。

### 杂项
#### 设置 locale
编辑`/etc/locale.conf` 加入`LANG=en_US.UTF-8`就 ok 了
#### 安装并设置输入法环境变量
安装输入法，
```bash
# pacman -Syu fcitx-im
```
咱准备用SDDM 所以修改` ～/.xprofile `加入如下三行
```bash
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

# 0x02 对平板的处理
按照上面的操作装是装好了，但这可是一块平板啊。咱要用触屏，屏幕旋转。于是为了体验咱装上了 kde( 
~~咱才不告诉你，我在平板上装上了i3wm，并且日用呢~~

## 1) 虚拟键盘
不管做什么，总是离不开键盘的，没有键盘就没法输入啊。咱们需要一个虚拟键盘，用于输入什么的，而且键盘还要好用，咱爪子比较大，太小的不好点。

#### 登录部分
我用了 SDDM ，所以这部分的虚拟键盘我用了 `extra/qt5-virtualkeyboard` 。先安装 qt5-virtualkeyboard，然后我们修改 `/etc/sddm.conf`这个配置文件。在`[General]`下面加入
```
InputMethod=qtvirtualkeyboard
```
改完之后大概是这样的
```bash
[marvelous@Horo-SSH  ~]$ cat /etc/sddm.conf
[Autologin]
Relogin=false
Session=
User=

[General]
HaltCommand=/usr/bin/systemctl poweroff
RebootCommand=/usr/bin/systemctl reboot
InputMethod=qtvirtualkeyboard

[Theme]
Current=breeze
CursorTheme=Adwaita

[Users]
MaximumUid=60000
MinimumUid=1000
```
#### 日常部分
嘛，上面提到了 qtvirtualkeyboard 这个这么好用的虚拟键盘，为什么我们登录之后就不用了呢？那是因为他是个输入法，我们不能用 fcitx 了，我需要 fcitx 来输入中文。于是我们在这里使用 `community/onboard`。装好之后启动就是了，开箱即用。

## 2）屏幕旋转
咱说了为了方便使用 kde 那么屏幕旋转咱们也要用配套的。安装 `iio-sensor-proxy-git` and `kded-rotation-git`，然后注销就好了。这两个包在 archlinuxcn 的仓库可以找到，兔兔已经帮咱们打好了，咱们就不需要从 aur 编译安装了。装好之后好像有什么不对劲的地方啊，是的，方向不对。这时候咱们需要补底裤了，为什么要补底裤？因为汝已经把她卖掉了（梗来自于systemd）。 咱们需要创建 `/etc/udev/hwdb.d/61-sensor-local.hwdb`并且按照格式填入对应的内容。下面是咱板子的：
```bash
[marvelous@Horo-SSH  ~]$ cat /etc/udev/hwdb.d/61-sensor-local.hwdb
sensor:modalias:acpi:KIOX000A*:dmi:*:svnTECLAST:pnDefaultstring:*
 ACCEL_MOUNT_MATRIX=0, 1, 0; 1, 0, 0; 0, 0, 1
```
然后执行
```bash
systemd-hwdb update
udevadm trigger -v -p DEVNAME=/dev/iio:deviceXXX
```
ok，方向正常了。  
如果是其他的设备呢？如果底裤自带的 `60-sensor-local.hwdb` 文件有就不需要了，而且理论上已经是正常的了。咱当时就是没有这个匹配上的。根据 systemd 给出的方法，查看对应的字符串
```bash
[marvelous@Horo-SSH  ~]$ cat /sys/class/dmi/id/modalias
dmi:bvn:bvrH5C5_A1:bd01/28/2016:svnTECLAST:pnDefaultstring:pvrDefaultstring:rvnAMICorporation:rnCherryTrailCR:rvrDefaultstring:cvnDefaultstring:ct3:cvrDefaultstring:
[marvelous@Horo-SSH  ~]$ cat /sys/`udevadm info -q path -n /dev/iio:device0`/../modalias
acpi:KIOX000A:KIOX000A:
```
对就是 这两条
```bash
cat /sys/class/dmi/id/modalias
cat /sys/`udevadm info -q path -n /dev/iio:device0`/../modalias
```
然后参考 [60-sensor-local.hwdb](https://github.com/systemd/systemd/blob/master/hwdb/60-sensor.hwdb)，新增内容写就好了，可以对比我给出来那个文件和相应的字符串，照葫芦画瓢就好了。
## 3）画面撕裂（解决不了）
用 modesetting 驱动在内屏（就是平板的那个屏幕）会看起来有很明显的撕裂,相比之下 intel 的那个看起来好点，所以这里我们使用`xf86-video-intel`，虽然这个驱动不在被推荐，但这寨板的显卡似乎挺旧的所以也只能用它了。安装 `xf86-video-intel`，然后修改 `/etc/X11/xorg.conf` 写入如下内容
```
Section "Device"
	Identifier "intel"
	Driver "intel"
	BusID "PCI:0:2:0"
	Option "AccelMethod" "sna"
	Option "TearFree" "True"
	Option "Tiling" "True"
	Option "DRI" "3"
EndSection
```
画面撕裂的问题会得到缓解。  
**更新：在内屏和外屏的情况下画面都是会出现撕裂。这个问题咱问了一下其他人，好像 atom 用的显卡都是这样的，所以如何取舍就看汝了。而且这样设置在咱使用外屏的输出时，会看见鼠标光标消失的问题，并且伴随绘图不正常。使用 modesetting 也会遇到这个问题。而上面的配置中把 DRI 设置2可以解决这个问题，但撕裂始终存在，而且拖动窗口会出现卡顿。**
## 4）视频硬解加速
z8300 cpu性能本身并不是很强，而且视频播放没硬解的话，会很耗电的，于是咱们需要硬解。  安装`libva-intel-driver`然后在`~/.xprofile`中加入
```bash
export LIBVA_DRIVER_NAME=i965
export LIBVA_DRIVERS_PATH=/usr/lib/dri/
export VDPAU_DRIVER=va_gl
```
并推荐安装`archlinuxcn/chromium-vaapi`，可以在使用 chromium 看b站的视频的时候，可以直接使用硬解。

# 0x03 插曲
汝看上面的描述其实也没什么，很快就可以搞定了。但由于寨板还是寨板由于做工设计等问题，咱在使用的过程中啊，平板唯一的usb口上有一个黑色的小东西击穿了。也就是说，这块平板不能充电和使用otg了！！！如图：

![pic1](/public/pic/things-about-x80plus-1.jpg)

## 原理
查看之后发现上面有 AM 这个印丝然后，初步判断是二极管，然后去淘宝搜了一下，确定是个二极管了。而且移除之后低下的 pcb 也有二极管的图示。那这个是什么呢，是一个叫TVS瞬变二极管。然后用关键字` TVS瞬变二极管` 和 `usb`作为关键字谷歌了一下，确实这是用来保护电路的，防止瞬间的脉冲对后面的电路的影响。这个是直接反接在 usb 的正负极上面的，从上，面的图中可以看出 + 这一极是连接了在外壳上的，我们知道外壳是地，然后联系现象不难猜出连接方式，网上的资料搜索也验证了这个猜想。这个管子是击穿了，也就是正负接在一起了，当然充电和otg都部工作了。
## 解决方案
嘛，知道了怎么连接的和这个玩野是做什么的，接下来就好办了。因为只是起最简单的保护作用，简单粗暴直接去掉就好了。对没听错直接去掉。但我这个时候出现了一个比较坑的地方，我去掉了之后只能otg，但不能充电。弄了半天才发现，我随手拿了一根数据线，这根数据线的头比较短，插进去之后并没有碰到触点，也就是说是断路，我瞎想了半天，才发现问题。嘛，强迫症还是要去买个这个来修，因为很多东西都知道了，所以参数也很好确定下来了，直接淘宝下单，换上搞定。
# 0x04 为什么我不愿意重装系统
其实，我当时还遇到了另一件事情，在我快完成所有的配置的时候，我使用 Syu 进行更新系统，这本来是很正常的事情，更新然后关机睡觉。然后意想不到的事情出现了，在安装包的过程中机器意外断电，（电量提示还有 50% 以上）我也不知道为什么会断电，这不重要。重要的是在安装的过程中断电了，我重新启动之后，大惊喜。内核恐慌了（kernel panic）。这时候怎么办？重装？我忙了大半天才把系统装好并且设置好诶，怎么可能重装？当然是掏出 Live cd 然后 chroot 进去修。嘛，这也是 arch 安装要这么麻烦的一个原因吧。把当时安装的包重新安装一次，然后吧部分出现问题的东西删掉之后，重启。熟悉的画面回来了。其实包括这一个插曲在内花了几天我才把东西搞定。其中最麻烦的是恢复到一个差不多一样的环境了，虽然说有配置文件可以直接复制但还是有很多的问题。由于机器不一样，有很多的文件还是要改动之后才能复制的，而且我很多时候忘了自己当时改过什么了。直到不顺手的时候。而且在复制配置文件的时候我发现有几个程序是不能直接复制的，如果直接把全部东西搬过去就会出现把新机器当成旧机器，于是要有选择的移动配置文件。我一般出现问题会选择去修而不是重装。重装在部分时候，你只是想以最简单的方式解决一些复杂的问题，这确实有效。但我们需要衡量一下成本，到底是重装然后重新配置一个相同的环境(并且有可能问题依旧存在)的成本高,还是找到问题去修复的成本高。这里的成本包括时间和金钱上的成本。对于我来说重装然后配置的成本是很高的，所以这就是我讨厌动不动就重装系统和为什么不愿意重装系统的原因。至于新机器要装新系统纯粹是想重新弄一下而已。

# 0x05 最后
在写这篇东西的过程中，我试了一下外接显示器，然后它又瓦特了，基本上贴吧上面见到的故障，我已经全部见到了。嘛，建议是买个口碑好一点的，做工好一点的平板吧。不然挺多问题的。2333, 现在有了这个就可以不用带着很重的笔记本到处跑了，十分的方便，还方便远程连接。

