---
title: "用PulseAudio将电脑的声音用手机放出来"
date: 2019-02-06T22:30:57+08:00
description: "Pulseaudio 其实有很多骚操作，那就来说说通过网络用手机播放电脑的声音吧！"
topics:
  - 新手教程
  - 杂乱的笔记
tags:
  - pulseaudio
draft:  false
---
恩，咱之前啊，在咸鱼上收了一个树霉派1B，是的，一个老旧的版本。那和今天咱要说的题目有什么关系呢？关系大着呢，如果没有这个小玩意，咱也不知道还能这样玩呢。
## 缘由
这个东西，其实咱一开始买回来也不知道用来做什么，直到那天我找到了大量的歌曲存到了U盘，于是想这个东西有音频接口那为什么不直接拿来当个播放器呢？于是熟练的装好了咱最爱的 moc 播放器，插上耳机。额。。。。似乎声音不太对啊，是的，树霉派的音频电路真的很差。汝可能会问，咱为什么不直接用手机播放呢？那是因为啊，咱手机的空间太小了存不下辣么多的歌曲。可咱还是想用这个小东西来做播放器，难道就没有办法了吗？
## PulseAudio Over TCP
在 linux 上有个叫 pulseaudio 的东西，没错就是它。它可以使用网络来把声音送出去，具体怎么做的汝可以去搜一下 pulseaudio over tcp ，咱也不太清楚。但既然有这么方便的功能，岂不是可以解决上面提到的问题？咱们就开始配置吧。
## 配置过程
### 手机端
手机这边咱用的是 android 。所以去play士多，直接安装 [Simple Protocol Player](https://play.google.com/store/apps/details?id=com.kaytat.simpleprotocolplayer) ，安装好之后可以看见一个十分简单的界面，上面有几个选项这样的。接着咱们只需要修改 ip 和 port 就行了，ip 为汝电脑的 ip ，端口可以取 12345 ，也可以随你喜欢。其他默认就好，如果汝在后面设置好之后发现有些卡顿，还可以适当的调整一下 `buffer size`。

### 电脑端
先安装好 pulseaudio ，接着使用`pactl list sources short`列出源，例如咱是这样的
```
0	alsa_output.pci-0000_00_1f.3.analog-stereo.monitor	module-alsa-card.c	s16le 2ch 48000Hz	SUSPENDED
1	alsa_input.pci-0000_00_1f.3.analog-stereo	module-alsa-card.c	s16le 2ch 48000Hz	SUSPENDED
```
找到输出的那个，然后输入(假设 source=0，如果不是请参照汝机器将0换为对应数值，port和上面设置的port一样)
```bash
pactl load-module module-simple-protocol-tcp source=0 record=true port=12345
```
恩，接着在手机那边点一下那个播放键就可以用了。如果汝还想持久化这个设置，可以修改 `/etc/pulse/default.pa` 并追加
```
load-module module-simple-protocol-tcp source=0 record=true port=12345
```
其实就是 pactl 后面那堆东西啦。然后重启一下 pulseaudio 就ok了。

### 小问题
因为咱是用树霉派来播放的，这小东西不带喇叭，没有这个问题。但如果是电脑，估计汝会发现电脑那边也在放歌。那怎么办呢，如果停用这个设备的话，那两边都会没有声音。咱们在这偷个懒，创建一个虚拟设备就好了，根据 [这里](https://unix.stackexchange.com/questions/174379/how-can-i-create-a-virtual-output-in-pulseaudio) 用
```
sudo modprobe snd_aloop
```
添加一个 loopback 设备到 ALSA ， pulseaudio 那边也会看见一个新设备，这样问题就解决了。

## 后记
这个操作需要网络环境比较好的情况，不然会一卡一卡的，不过在家里面躺在床上用这个方法听听歌也是足够了。这仅仅是一个小技巧，可以在不想购买新的声卡的情况下，使用 pulseaudio 来转发声音，来避免声卡输出底噪过大之类的问题。也算是一个比较实用的技巧吧。咱听说，隔壁 fc 老师因为没有蓝牙设备，使用树霉派做了音频和 usb 的转发，不知道是怎么做到的呢，好想知道啊。

