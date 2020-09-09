---
title: "忻風隨身 PM2.5 檢測儀開箱和拆解"
date: 2020-09-04T18:27:22+08:00
description: "在？來點電子垃圾！據說拆裏面的傳感器都能回本，那就買幾個回來拆開康康。"
topics:
  - 杂乱的笔记
tags:
  - ble
draft: false
---
---
> 喫個飯？  
> 忍住！要省錢  
> 買個衣服？  
> 忍住！要省錢  
> 換個手機？  
> 忍住！要省錢  
> 來買電子垃圾！
> 來來來！多少錢  
>
---
## 0x00 買
一大早起來看見有人發了一個神祕鏈接給咱，咱一看隨身 pm2.5 檢測儀，而且還只需要12元一個。擺明了是那種陳年的貨，連接用的 app 都是年久失修的那種，賣這麼低就是當電子垃圾賣的嘛。看了一下評價，好東西，這裏面的傳感器要70一個。嘛，之前就打算買一個這種傳感器來康康，馬上下單了兩個。咱沒有空氣淨化機，所以說實話這東西其實並沒多大用處。但後面弄了一下才發現賣汝12元都是虧了。忻風隨身 PM2.5 檢測儀這個名字太長了，在咱購買的那家 X 寶店鋪的評論，下面有人把這東西當成自行車車燈使用，於是下文將此檢測設備稱爲自行車車燈，簡稱車燈。

## 0x01 開箱
過了幾天車燈就到了咱的手上了。包裝是這樣的，兩個都是白色的。這部分咱就只放圖了。開機之後那個圈會亮燈，看燈就可以知道空氣pm2.5大概的狀態了。值得一提的是這個是使用是激光進行測定，所以這東西適合家用或者當一個玩具，數值可能沒那麼的準確，但確實可以在一定的程度上反應汝周圍的空氣質量。
![mops_00.jpg](/public/pic/mops_00.jpg)
![mops_01.jpg](/public/pic/mops_01.jpg)
![mops_02.jpg](/public/pic/mops_02.jpg)
![mops_03.jpg](/public/pic/mops_03.jpg)
![mops_04.jpg](/public/pic/mops_04.jpg)
![mops_05.jpg](/public/pic/mops_05.jpg)
![mops_06.jpg](/public/pic/mops_06.jpg)

## 0x02 拆
車燈沒有螺絲口，都是卡扣，直接拿個敲棒翹就是了。用力滑一圈就打開了。
![mops_07.jpg](/public/pic/mops_07.jpg)

然後看見傳感器和電池直接放在一個架子上，架子也是用東西卡住，直接壓一下抽出來既可。
![mops_08.jpg](/public/pic/mops_08.jpg)

在拔掉了電池和傳感器後就可以看見主板了。可以看見是一個 nRF51822 的藍牙芯片。
![mops_09.jpg](/public/pic/mops_09.jpg)
![mops_10.jpg](/public/pic/mops_10.jpg)

卸掉板上的螺絲，就可以把正面的按鈕拆下來了，把主板反過來可以看見一排的 LED。
![mops_11.jpg](/public/pic/mops_11.jpg)
![mops_12.jpg](/public/pic/mops_12.jpg)

好這就是全部了。因爲傳感器還要用所以傳感器就不拆了。

## 0x03 獲取數據
嘛，那個 app 和之前一樣，咱是不敢用的了，既然它是藍牙的那麼就說明咱們可以拿到數據了。那麼直接拿電腦連接，先不管有沒有加密，先試試可以拿到什麼再說。掃描的時候可以發現這個車燈，沒有像之前米家的那個一樣使用廣播，那就直接連吧。（順便記錄一下使用方法，免得下次又忘了）  
直接使用電腦連過去，那麼就直接用工具 `bluetoothctl` 。(下面和 mac 地址有關的將被替換爲 xx）。  
先進行掃描，然後估計可以發現這麼一個 device
```
Device C2:FD:DA:xx:xx:xx 测霾单品
```
然後鏈接它，接着提示符會變爲`[测霾单品]#`，並且噴出一大堆內容。接着使用` menu gatt `進入 submenu。
```
[测霾单品]# menu gatt
Menu gatt:
Available commands:
-------------------
list-attributes [dev/local]                       List attributes
select-attribute <attribute/UUID>                 Select attribute
attribute-info [attribute/UUID]                   Select attribute
read [offset]                                     Read attribute value
write <data=xx xx ...> [offset] [type]            Write attribute value
acquire-write                                     Acquire Write file descriptor
release-write                                     Release Write file descriptor
acquire-notify                                    Acquire Notify file descriptor
release-notify                                    Release Notify file descriptor
notify <on/off>                                   Notify attribute value
clone [dev/attribute/UUID]                        Clone a device or attribute
register-application [UUID ...]                   Register profile to connect
unregister-application                            Unregister profile
register-service <UUID> [handle]                  Register application service.
unregister-service <UUID/object>                  Unregister application service
register-includes <UUID> [handle]                 Register as Included service in.
unregister-includes <Service-UUID><Inc-UUID>      Unregister Included service.
register-characteristic <UUID> <Flags=read,write,notify...> [handle] Register application characteristic
unregister-characteristic <UUID/object>           Unregister application characteristic
register-descriptor <UUID> <Flags=read,write...> [handle] Register application descriptor
unregister-descriptor <UUID/object>               Unregister application descriptor
back                                              Return to main menu
version                                           Display version
quit                                              Quit program
exit                                              Quit program
help                                              Display help about this program
export                                            Print environment variables
[测霾单品]#
```
鍵入`list-attributes`後可以看見 
```
Primary Service (Handle 0x0009)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0008
	00001801-0000-1000-8000-00805f9b34fb
	Generic Attribute Profile
Characteristic (Handle 0xa084)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0008/char0009
	00002a05-0000-1000-8000-00805f9b34fb
	Service Changed
Descriptor (Handle 0x0015)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0008/char0009/desc000b
	00002902-0000-1000-8000-00805f9b34fb
	Client Characteristic Configuration
Primary Service (Handle 0x2e80)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service000c
	0000180a-0000-1000-8000-00805f9b34fb
	Device Information
Characteristic (Handle 0x1c14)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service000c/char000d
	00002a29-0000-1000-8000-00805f9b34fb
	Manufacturer Name String
Primary Service (Handle 0x2e80)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service000f
	00001530-1212-efde-1523-785feabcd123
	Vendor specific
Characteristic (Handle 0x2f54)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service000f/char0010
	00001532-1212-efde-1523-785feabcd123
	Vendor specific
Characteristic (Handle 0x2f54)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service000f/char0012
	00001531-1212-efde-1523-785feabcd123
	Vendor specific
Descriptor (Handle 0x0015)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service000f/char0012/desc0014
	00002902-0000-1000-8000-00805f9b34fb
	Client Characteristic Configuration
Characteristic (Handle 0x57e8)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service000f/char0015
	00001534-1212-efde-1523-785feabcd123
	Vendor specific
Primary Service (Handle 0x2e80)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017
	6e400001-b5a3-f393-e0a9-e50e24dcca9e
	Nordic UART Service
Characteristic (Handle 0x6434)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018
	6e400003-b5a3-f393-e0a9-e50e24dcca9e
	Nordic UART RX
Descriptor (Handle 0x0015)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018/desc001a
	00002902-0000-1000-8000-00805f9b34fb
	Client Characteristic Configuration
Characteristic (Handle 0x8248)
	/org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char001b
	6e400002-b5a3-f393-e0a9-e50e24dcca9e
	Nordic UART TX
```
接着`select-attribute 6e400003-b5a3-f393-e0a9-e50e24dcca9e`和戳一下,就可以看見返回的值。
```
[测霾单品]# select-attribute 6e400003-b5a3-f393-e0a9-e50e24dcca9e
[测霾单品:/service0017/char0018]# notify on
[CHG] Attribute /org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018 Notifying: yes
Notify started
[CHG] Attribute /org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018 Value:
  aa 50 06 00 20 00 00 d1 01 d4 f1 02 d1 01 d4 f1  .P.. ...........
  50                                               P
[CHG] Attribute /org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018 Value:
  aa 54 00 0c 00 01 00 01 0c                       .T.......
[CHG] Attribute /org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018 Value:
  aa 50 06 00 20 00 00 d1 01 d4 f1 02 d1 01 d4 f2  .P.. ...........
  51                                               Q
[CHG] Attribute /org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018 Value:
  aa 50 04 32 32 00 00 00 00 00 00 00 00 00 00 00  .P.22...........
  00 00 00 62                                      ...b
[CHG] Attribute /org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018 Value:
  aa 50 06 00 20 00 00 d1 01 d4 f1 02 d1 01 d4 f2  .P.. ...........
  51                                               Q
[CHG] Attribute /org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018 Value:
  aa 50 05 00 00 c5 11 00 00 02 75 00 00 00 00 00  .P........u.....
  00 4c                                            .L
[CHG] Attribute /org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018 Value:
  aa 50 06 00 20 00 00 d1 01 d4 f1 02 d1 01 d4 f3  .P.. ...........
  52                                               R
[CHG] Attribute /org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018 Value:
  aa 50 07 00 0a 00 0b                             .P.....
[CHG] Attribute /org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018 Value:
  aa 50 06 00 20 00 00 d1 01 d4 f3 02 d1 01 d4 f4  .P.. ...........
  55                                               U
[CHG] Attribute /org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018 Value:
  aa 54 00 0c 00 01 00 01 0c                       .T.......
[CHG] Attribute /org/bluez/hci0/dev_C2_FD_DA_XX_XX_XX/service0017/char0018 Value:
  aa 50 06 00 20 00 00 d1 01 d4 f4 02 d1 01 d4 f4  .P.. ...........
  56                                               V
```
很好，到這裏就是湊合着，因爲咱沒有去看那個 app 因此不知道數據的格式因此我們只能去猜。
整理之後發現`aa 50 06` 開頭的後兩位爲 pm2.5 。 `aa 50 04` 開頭的後兩爲電池電量百分比。`aa 50 07` 開頭的後面的內容似乎是個時間。其餘的咱不知道。好像有人看了一下 android 的 app 然後把協議弄明白了，到時候去看那個吧。咱問了一下他反回來的只有 pm2.5 的數據嗎？他說反正 app 裏面只有 pm2.5。 注意咱在前面發的說明書的圖，那個說明了傳感器有別的參數可以測出來。也就是說這個的固件沒返回這些值回來。嘛，一開始買回來就只是打算拆傳感器用的就沒想這些。 等等，既然已經知道了數據格式，那這一次怎麼不像上一次那樣寫個小腳本然後寫道數據庫裏面啊。原因很簡單，這個不是最終的，可能還有別的變動。

## 0x04 最後
等一下，這怎麼還不是最終的呢。對沒錯，如果只是湊合着用那大可不必繼續搞下去，甚至汝可以用那個 app 直接看。但是，咱前面說了這東西 12 元買給汝都虧了。這車燈其實 ble dfu 沒有驗證簽名，這是某個裙友告訴咱的。對，汝沒有聽錯 dfu 沒有簽名！這得感謝廠商，沒有使用簽名。換句話說就是，只要咱們能隨便的刷自己的固件，而且刷的辦法十分簡單就是用工具從藍牙把固件傳上去就是了。接下來只要搞清楚對應的 pin 之間的關係，那麼就可以直接重寫整個固件，這意味着可玩性大大大大的提高。當然咱也是第一次接觸這個 nRF51822 芯片，也要慢慢學，因此有沒有下一篇就不清楚了，但目前湊合用的話就到這裏了爲止了。如果汝想接入那個 home assistant 的話，可以等羣裏面的大佬把協議整理出來之後按照格式寫一下就接近去就好了，但目前咱只能做到這裏。（咱可能會更新附上對應的鏈接）。以上大概就是這一次撿垃圾的做了的事情，感謝汝看到最後。

## 0x05 更新
~~測了一下，08 09 10 腳是連着那 led 的，估計你補藍色的 led  和缺的元件就有 RGB 了，然後上面四個焊盤，正對着芯片的方向看從左到右是 swclk swdio gnd 3.3V 。~~ 順便提一句，在折騰時候發現咱買了兩個，但這兩個的 mac 地址的前3位不是固定的。
### 網頁艾爬爬
上面提到了那個 app 已經年久失修了，服務器也登錄不上了。甚至在更新這一段話(2020-09-09)的時候，產品背面的下載鏈接也失效了。但是，在九月( @septs )的努力下，有一個網頁版本的 app 替代品，這個網頁實現了 app 裏面幾乎所有的功能，包括 X 寶評論說的那個壞掉的間隔檢測。  
https://nicelabs.github.io/mops-vida-pm-watchdog/  
具體用起來可以見下圖。  
![mops_webapp.jpg](/public/pic/mops_webapp.jpg)
只需要裝個 chromium 或者 chrome 就可以使用了。Android 上是可以直接使用的。至於 Linux 下面這裏提一下，WEB Bluetooth 遇到的一些問題。  
首先汝要解決的問題是汝的用戶不需要特權就可以訪問到 ble ，至於怎麼做這個就要去搜一下就知道了（請不要使用 root 權限運行瀏覽器 ！）。接着需要打開 `chrome://flags/#enable-experimental-web-platform-features` 這個一項，如果不打開的話，當你點擊連接的時候是不會有什麼反應的。在做好這兩件事之後應該就是，開箱即用了。至於還有個坑就是一旦配對過之後可能會搜不到，這時候只需要在 bluetoothctl 裏面 remove 一下那個車燈就好了。

### PINOUT
這裏寫出部分，具體可以看整理後的 [PINOUT.md](https://github.com/NiceLabs/mops-vida-pm-watchdog/blob/master/docs/PINOUT.md)
#### SWD
![mops_swd.jpg](/public/pic/mops_swd.jpg)

#### LED
|     PIN | Note                     |
| ------: | ------------------------ |
| `P0.10` | Red leds in the circle   |
| `P0.09` | Green leds in the circle |
| `P0.22` | Bluetooth status         |
| `P0.21` | Power status             |

#### Sensor TX & TR
|     PIN | Note   |
| ------: | ------ |
| `P0.05` | PMS Tx |
| `P0.04` | PMS Rx |

### PROTOCOL
九月把 protocol 都弄出來了在這裏 [protocol-design.md](https://github.com/NiceLabs/mops-vida-pm-watchdog/blob/master/docs/protocol-design.md)。如果不理解其實汝可以打開上面提到的網頁然後連車燈然後用 wireshark 來抓藍牙看看，對着看就懂了。
具體咱也懶得寫了，舉個例子上面拿到了一段這樣的返回。
```
  aa 50 06 00 20 00 00 d1 01 d4 f1 02 d1 01 d4 f1  .P.. ...........
  50                                               P
```
對着那個文檔，咱們可以知道回包的第一位 aa 表示的是 Magic Header ，第二位 50 表示的是信息類型那麼就對着看 50 表示的是什麼， 50 是 Update Packet ， 那個第三位 06 ，對着看就知道表示後面返回來的 Sensor 的數據了。  
基於前面提到的，如果想接入 home assistant 之類的軟體的話，有了這份 protocol 就可以開始寫了。因爲咱不需要這個所以就不寫了。

### 個人折騰遇到的問題
- 鏈接調試器的時候要把 usb 連上提供電源，不然可能會連不上。
- 在 dump flash 的時候發現了這個芯片的程序有讀保護，dump 出來的數據全爲 0x00 。搜了一下發現有一篇 [NRF51822 code readout protection bypass a how to](https://www.pentestpartners.com/security-blog/nrf51822-code-readout-protection-bypass-a-how-to/)。利用 pc 和 r3 寄存器一點一點的將 flash 裏面的內容讀出來，挺有意思的可以去康康。

---
## 參考文章以及鏈接
~~無（可能後續會添加）~~
- https://nicelabs.github.io/mops-vida-pm-watchdog/
- https://github.com/NiceLabs/mops-vida-pm-watchdog/blob/master/docs/protocol-design.md
- https://www.pentestpartners.com/security-blog/nrf51822-code-readout-protection-bypass-a-how-to/



