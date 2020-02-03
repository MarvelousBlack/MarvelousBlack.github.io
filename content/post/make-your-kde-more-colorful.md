---
title: "Make Your Kde More Colorful"
date: 2019-04-22T21:31:27+08:00
description: "想要想某個桌面一樣自動根據顏色變化的 KDE 嗎？那就跟着做吧。"
topics:
  - 新手教程
  - 杂乱的笔记
  - 軟體推薦
tags:
  - KDE
  - 軟體推薦
draft:  false
---
想要跟隨畫布顏色變化的kde桌面嗎？那麼就快來用這個叫 [kcm-colorful](https://github.com/IsoaSFlus/kcm-colorful) 的小程序吧！先放張我自己的桌面截圖吧。
![make-your-kde-more-colorful](/public/pic/make-your-kde-more-colorful.png)

## 軟件介紹和食用方法
這個咱的一個朋友，三頭怪寫的，軟體的描述是根据当前的壁纸改变KDE Plasma的颜色。正如你在上面所看到的那樣整個界面都是根據畫布的顏色配置出來的，咱這裏的畫布是粉色的於是就可以看見其他地方都是粉色的。爲了方便使用這個有這麼一個界面：  
![make-your-kde-more-colorful1](/public/pic/make-your-kde-more-colorful1.png)
方便你直觀的直接選取備用顏色和看見你的壁紙。當然這個界面已經支持暗色主題了。汝可能發現了咱這裏的預覽圖和咱的壁紙不一樣啊，那是因爲咱有兩個顯示器，而兩個顯示器設置的壁紙不一樣於是這裏顯示的就是另一個顯示器的壁紙了。那咱就沒法選了另一張圖了啊，先別慌，作者還提供了一個叫 `kcmcolorfulhelper` 這個名字的 cli 工具用於這種情況的處理。讓咱們先看看幫助吧  
```bash
[marvelous@Vanilla  ~]$ kcmcolorfulhelper -h

Usage: kcmcolorfulhelper [options]

Helper for kcm-colorful.

Project address: https://github.com/IsoaSFlus/kcm-colorful



Options:

  -h, --help               Displays this help.

  -p, --picture <file>     Picture to extract color.

  -c, --color <colorcode>  Set color manually, eg: #1234ef.

  -s, --set-as-wallpaper   Set picture specified by "-p" as wallpaper.

  -d, --debug              Show debug info.

  -n, --number <int>       Select the Nth color in candidate list. Default is

                           1.
```
使用上也很簡單，直接加 `-p` 和圖片就好了，會自動應用可以順便加上 `-s` 一次性把畫布也設置上。

## 安裝
這個就簡單了，如果添加了 CN 源，汝可以直接
```bash
sudo pacman -S kcm-colorful-git
```
如果沒有，那麼汝還能從 aur 中找到這個包安裝。
抱歉，我不是 Arch 。 沒事，咱們還能手動安裝啊。

```
git clone --recursive https://github.com/IsoaSFlus/kcm-colorful.git
cd kcm-colorful
mkdir build
cd ./build
cmake ../
make
sudo make install
#Then set your plasma desktop theme to "Colorful" for best experience.
```

## 後續配置
好了，配置好了怎麼似乎有些不太一樣啊，因爲上面只是說了用法和安裝並沒有說怎麼配置啊。首先在 KDE `系统设置->桌面行为->桌面特效`，找到“模糊”项并使能。之後打開 `系统设置->工作空间主题->桌面主题`，设置主题为`Colorful`。恩，估計汝已經可以看見效果了，如果需要像上面的截圖那樣那就需要繼續調整了。安裝 [BreezeBlurred](https://github.com/alex47/BreezeBlurred) 這個主題，CN源也對這個主題打包了，名字可能有一點點變化，汝可以搜索一下。接着 `系统设置->应用程序风格->窗口装饰` 找到一個叫微風的主題，那個預覽圖透明的那個，打開設置一下透明度就完成了。這一步的圖文教程可以在[戳這裏](https://github.com/IsoaSFlus/kcm-colorful/blob/master/README.md)找到。

---
#### 引用以及參考
https://github.com/IsoaSFlus/kcm-colorful
