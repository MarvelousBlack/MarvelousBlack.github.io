---
title: "關於 USB OTG 的一些思考"
date: 2019-04-22T21:30:30+08:00
description: "之前整了個寨版於是就在淘寶上面買了一個據稱可以一遍充電一遍使用 otg 的轉換頭。於是就在這裏記錄一下。其實咱也不知道咱在說些啥。"
topics:
  - 杂乱的笔记
tags:
  - USB
draft:  false
---

之前整了個寨版於是就在淘寶上面買了一個據稱可以一遍充電一遍使用 otg 的轉換頭。看了一下結構，於是就在這裏記錄一下。其實咱也不知道咱在說些啥。

## OTG 是個啥？
根據 wiki 上面的描述, OTG 是個這個東西。

> USB On-The-Go, often abbreviated to USB OTG or just OTG, is a specification first used in late 2001 that allows USB devices, such as tablets or smartphones, to act as a host, allowing other USB devices, such as USB flash drives, digital cameras, mice or keyboards, to be attached to them. Use of USB OTG allows those devices to switch back and forth between the roles of host and device. A mobile phone may read from removable media as the host device, but present itself as a USB Mass Storage Device when connected to a host computer.

簡單的說就是可以使 USB 設備從 USB 周邊設備，變成 USB 主機。但市面上大多數的 OTG 線都是不帶供電的，也就是汝只能充電，或者只能使用 OTG。

## 號稱可以一遍充電一邊使用的轉接頭是怎麼做到的？
上面提到了不能一遍充電一邊 otg ，那麼市面上的線是怎麼做到的呢？ 嘛，因爲之前提到那塊寨版只有一個 USB 口，所以咱就打開萬能的淘寶買了一個，也好奇結果於是隨手拆了。拆開之後發現有個簡單的綠色 PCB ，一個開關，一顆68千歐姆的電阻，還有一根只有vcc 和 gnd 的電源輸入線直接連這板上的 vcc 和 gnd 然後就沒了。是的十分簡陋。那個開個控制 USB 線上面的 ID 引腳，ID 引腳是後來加進去用於判斷是主機還是從幾用的沒，也就是說用來判斷十否使用 otg。那開關和 那個電阻是用來幹嘛的呢。咱看了一下商品介紹有這個東西啊有兩個模式，一個是普通的 otg 另一個就是那個可以充電的模式。關於那個電阻咱在wiki下面找到了這樣的一段話：

> Three additional ID pin states are defined at the nominal resistance values of 124 kΩ, 68 kΩ, and 36.5 kΩ, with respect to the ground pin. These permit the device to work with USB Accessory Charger Adapters that allows the OTG device to be attached to both a charger and another device simultaneously.

> These three states are used in the cases of:

> A charger and either no device or an A-device that is not asserting VBUS (not providing power) are attached. The OTG device is allowed to charge and initiate SRP but not connect.  
> A charger and an A-device that is asserting VBUS (is providing power) are attached. The OTG device is allowed to charge and connect but not initiate SRP.  
> A charger and a B-device are attached. The OTG device is allowed to charge and enter host mode.  
>

也就是說有這麼三個電阻阻值，124kΩ，68kΩ和36.5kΩ，分別對應用於充電的可以引導 SRP，不引導 SRP 充電但可以 otg 並且提供電流鏈接，和只能 OTG 鏈接,某些偷懶的線材中 ID 引腳是直接接地的。那個就剩下最後一個問題了，爲什麼需要一個開關呢？這是因爲某些手機的電源芯片並不管這個可以充電的狀態，於是我們需要一些小技巧先欺騙一下電源管理芯片，先例用純 otg 的狀態進入 otg 然後利用那個開關切換狀態，引入外部供電。然後就可以開啓一遍 otg 一邊充電的狀態了。

---

#### 參考和引用
https://en.wikipedia.org/wiki/USB_On-The-Go
