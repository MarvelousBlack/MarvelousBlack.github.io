---
title: "車燈（忻風隨身 PM2.5 檢測儀）折騰筆記"
date: 2021-01-27T12:56:34+08:00
description: "對！沒錯，這就是上一篇文章的後續。"
topics:
  - 杂乱的笔记
tags:
  - ble
draft: false
---

A,距離上一片應該過去好幾個月了，重寫這車燈是不太可能的了，咱是不太想繼續弄了，現在最多只是做到了，把數據發回和電量顯示，這已經滿足咱的日常需求了，所以可能就不會繼續弄下去。而且隔壁羣友已經重新畫了塊pcb,把主控換成了esp8266，燈換成了 R! G！ B！ (炫彩車燈）,甚至還把上面的巨大按鈕換成了某智能燈的開關，做到了半夜起牀也不怕找不到按鈕，所以重寫也沒什麼意義了。 那這篇東西就是講咱折騰這玩意遇到的問題，當然都是一些很簡單的問題，但最近沒什麼可以水，那就把這個寫一些吧。  

# 0x01 點燈
之前提到了車燈這個東西是用的主控是 nRF51822 , 而開發這個最快的方法是使用 SDK 進行開發。恩，配置好環境，先點個燈,找到裏面的示例文件， make 然後刷入。誒，怎麼還是黑的什麼反應都沒有。仔細的閱讀了 SDK 的文檔之後發現有下面的一段話。

> Enabling support for a board
> To enable support for an older Nordic Semiconductor board or a custom board, you must include a define statement for the board that you want to use before you compile the code.

> According to the define statement, the suitable board support file is selected. The board support file defines the peripherals, thus the location of LEDs and buttons, for the selected platform. The header files for supported boards are located in the directory examples\bsp. The actual selection of the file according to the define statement is done in boards.h.

> Depending on the device on the legacy board, you might need to change the memory layout. For example, all nRF51 examples assume that you are using the 32 kB variant of nRF51, so if you are using a variant with 16 kB RAM, you must decrease the size of IRAM1 by 16 kB (0x4000 in hex). In Keil, click Project > Options for Target '...' and modify the values for "Read/Write Memory Area". For GCC, change the linked *.ld file in the Makefile.

根據 SDK 裏面的描述，也就是 nRF51 有多個型號，而示例文件假定使用的是 32kB 的版本，如果是使用的是 16kB 的版本則需要修改地址的位置。查看數據手冊， nRF51822 有 AA 和 AC 版本， 車燈上面的是 AA 是最低配的，只有 16 kB 的 RAM 。因此需要修改 ld 文件。把 MEMORY 這段修改爲下面這樣。重新 make 然後 flash ，終於把燈點起來了。不得不說這車燈，用的乞丐版的 nRF51822 的內存實在是太小了！

```
MEMORY
{
  FLASH (rx) : ORIGIN = 0x1b000, LENGTH = 0x25000
  RAM (rwx) :  ORIGIN = 0x20001fe8, LENGTH = 0x2018
}
```

# 0x02 BLE
燈點起來了，那再試試藍牙點燈，打開示例文件修改好 ld 文件， make ，flash ！ 怎麼又黑了，找了一下，是停在了藍牙初始化。搜了一下，發現了另一個硬件的差異。在上一篇的文章裏面可以中在高清大圖中，可以看見這個芯片的 P0.26 P0.27 兩個腳鏈接到了空焊盤上了，這兩個腳從數據手冊中看是用於鏈接一個外部的 32.768kHz 的晶振用的。看代碼註釋知道 SoftDevice 需要低頻時鐘，因此需要用到一個 32.768 kHz 的時鐘，而 SDK 的示例默認使用的是外部時鐘。爲了確認官方開發板上是有這個外部晶振了，咱特地搜了一下官方開發版的圖，發現確實是有這個晶振的。所以咱們用默認的代碼去使用這個車燈不存在外部的晶振，當然起不來。知道有這個問題就簡單了，因爲拿到手的時候這個藍牙是可以使用的，那麼就說明有辦法解決這個問題。翻數據手冊，可以使用內部時鐘，因此修改對應的 config 頭文件，把 NRF_CLOCK_LFCLKSRC.source 改爲 NRF_CLOCK_LF_CLK_RC 像下面這個。

```
// Low frequency clock source to be used by the SoftDevice
#ifdef S210
#define NRF_CLOCK_LFCLKSRC      NRF_CLOCK_LFCLKSRC_XTAL_20_PPM
#else
#define NRF_CLOCK_LFCLKSRC      {.source        = NRF_CLOCK_LF_SRC_RC,            \
                                 .rc_ctiv       = 16,                                \
                                 .rc_temp_ctiv  = 1,                                \
                                 .xtal_accuracy = NRF_CLOCK_LF_XTAL_ACCURACY_20_PPM}
```
重新編譯然後刷入，完成！可以用藍牙了。

# 0x03 ADC 部分
其實這個只是咱沒注意看，電池的電量是直接對電池的電壓進行測定得出的，對於這個使用的鋰電池，滿電壓測的應該是 4.2V 左右，那測用於電池輸入的應那邊的引腳，可以發現電壓大概爲2點多，也就已經進行過一次分壓了，但是去看數據手冊可以發現 ADC 的對比電壓是 1.3V ，也就是只要高於 1.3V 就沒法測出。 但仔細的看數據手冊實際上是有個內部的分壓電阻選項的，有好幾個選項，1/3 分壓 2/3 分壓。rg 了一下 SDK 可以發現對應的註釋，按照註釋的說明設置一下對應的量，之後就可以重新算出電池的電壓爲多少了。

# 0x04 總結
嘛，總的來說還是挺好玩的，雖然這個芯片的 SDK 真的有點難用，但這也算是咱第一次接觸到這種藍牙的芯片。雖然最後因爲懶不想繼續做下去了，但在這個過程中還是學到很多東西，最最最重要的是好好讀數據手冊！這個真的可以減少很多低級的問題。

---
