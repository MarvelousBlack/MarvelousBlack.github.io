---
title: "Google Chinese Handwriting IME 简单了解和使用"
date: 2019-02-06T23:35:39+08:00
description: "突然接到一位不愿意透露姓名的人士请求打包，于是了解了一下这个神奇的用Electron 寫的利用 Google Translate 的手寫輸入法。"
topics:
  - 杂乱的笔记
tags:
  - Handwriting input
draft:  false

---
在一个月黑风高的晚上，一位不愿意透露姓名的 fc 老师[请求](https://github.com/archlinuxcn/repo/issues/1032) 打一个叫做`google-chinese-handwriting-ime`的包，于是咱去看了看，看起来很不错的样子，于是试了一下这个神奇的用 Electron 写的利用 google translate 的手写输入法。这个输入法的利用的是笔记本的触摸板来当做手写输入，十分适合有手写输入需求的人。而且写这篇东西的时候，恰好是春节，可以回家装一个给父母老人使用，十分的简单易操作。~~好像有什么不对劲的地方~~

## 打包
这个[软件](https://github.com/Saren-Arterius/google-chinese-handwriting-ime)其实已经有[aur](https://aur.archlinux.org/packages/google-chinese-handwriting-ime/)的包，但汝会发现这个包其实是打不出来的，因为 depends 少了一个 `git`，然后咱尝试直接使用 Release 里面的 v3.0.7 进行打包，打完出来之后发现是一闪一闪的无法使用，看了一下 commits 有个更新，于是直接打了个 -git 包，恩，这个不闪，能用了。这个包已经在 cn 源里面了，可以直接安装 `google-chinese-handwriting-ime-git` 来使用。

## 咱的理解
正如那个软件的描述所说这是一个用Electron 写的手写输入法。它十分巧妙的利用了 google translate 的手写识别，从而做到了比较高的识别率。为什么说巧妙的利用了呢？ 汝用过 google translate 的话就会发现那个手写识别只在那个网页有效，但用了这个汝可以在其他地方使用。而且这里利用到了触摸板，不要以为触摸板的移动是相对的就认为这个是个相对输入设备，其实人家是绝对定位的啦，部分人可能见过有人用触摸板来玩 osu！吧。对，因为部分的触摸板是绝对定位，所以可以做到和手写笔差不多的光标移动。而且手写输入法不要求压感，于是就可以用来做手写输入。

## 安装和配置
arch 的话直接用 cn 源里面那个就好了，可是要用这个输入法需要配置许多的东西。先将汝添加到 `input` 组里面，然后开始配置触摸板，修改 `/opt/google-chinese-handwriting-ime/config.js`。运行 `evtest` 选择你的触摸板找到最左上角和最右下角的坐标，然后修改对应的 `touchpad_support.touchpad_coords.max.x`和`touchpad_support.touchpad_coords.max.y` 数字可以适当的调整一下，咱当时测试的时候发现这个数值要比测出来的大好多才能正常的工作，所以调整到汝适合的大小。还要将如下字段中的 null 换成对应的设备数字。
```
    preferred_device_id: {
      xinput: null,
      dev_event: null
    },
```
可以分别用 `xinput list` 和 `evtest` 查看。这样就大概配置好了， **如果是 kwin 的话还需要将焦点策略调整为点击以取得焦点。**

## 使用体验
配置好之后直接运行这个输入法就好了，直接在触摸板上面写字，还是蛮方便的，准确率是比较高的。但咱在测试完可以使用之后就没在用过了，因为焦点策略不能是跟随鼠标所以和咱的习惯冲突了。但这个可以利用触控板来手写，可以少买一个手写设备了，而且大多数人笔记本都是外接鼠标的，直接使用触控板是非常的方便的，可以再一次利用笔记本上的触摸板，还是蛮推荐有手写输入需求的人使用的。

---
## 软件地址
Google Chinese Handwriting IME 输入法：https://github.com/Saren-Arterius/google-chinese-handwriting-ime
