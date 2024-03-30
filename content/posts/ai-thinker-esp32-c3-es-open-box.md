---
title: "安信可 ESP32-C3 開發板工程樣品開箱"
date: 2021-05-04T10:26:47+08:00
draft: false
description: "淘寶上搜了一下發現有就下單了，等了十幾天終於到手了。"
topics:
  - 杂乱的笔记
tags:
  - ESP32C3
  - ble
  - wifi
---
額，canokey 的那篇在寫了，因爲中間斷斷續續，所以可能還要再等等。  
這次買到的模塊和開發板都是工程樣品，可能和最後的版本有點區別。

## 0x00 爲啥買 ESP32-C3
很簡單，便宜，幾塊錢，而且還是新品（x  
當然買之前還是要看一下之後能不能用的上，所以咱還翻了一下 Datasheet。 這東西提供的引腳很少，感覺是個 esp8266 的替代品，但它多了一個 BLE(LE) ，多個 BLE（LE）就可以多很多玩法了，而且 ESP32C3 支持 IOMUX 也就是可以自由的分配引腳功能，還是方便的，綜上先搞點來玩玩看。

## 0x01 開箱圖
拿到手之後是這樣的，這次買的是安信可的模塊,所以有個殼子，而咱可以工具打開，所以湊合着看吧。
包裝裝的很好，都拿防靜電袋子裝着。
![ai-thinker-esp32-c3-es-open-box_0](/public/pic/ai-thinker-esp32-c3-es-open-box_0.jpg) 
![ai-thinker-esp32-c3-es-open-box_1](/public/pic/ai-thinker-esp32-c3-es-open-box_1.jpg) 
![ai-thinker-esp32-c3-es-open-box_2](/public/pic/ai-thinker-esp32-c3-es-open-box_2.jpg) 
![ai-thinker-esp32-c3-es-open-box_3](/public/pic/ai-thinker-esp32-c3-es-open-box_3.jpg) 
![ai-thinker-esp32-c3-es-open-box_4](/public/pic/ai-thinker-esp32-c3-es-open-box_4.jpg) 
可以看見開發板上面有兩個單色的 LED 燈和一個 5050 **RGB** 燈。單色的 LED 接在了 IO 18 和 IO 19 上，5050 RGB 的顏色接在 IO 3 4 5 上， LDO 用的是 AMS1117-3.3 , 串口使用的是 CH340C。

## 0x02 上電
由於模塊咱沒有底板也懶得焊接，所以留着日後使用。直接拿開發板插電腦。先找個簡單的例子少進去，按照 [esp32c3/get-started](https://docs.espressif.com/projects/esp-idf/en/latest/esp32c3/get-started/index.html) 的步驟，設置好環境然後燒錄就好了。然後打開 monitor 看見輸出：
```
Restarting now.
x���ESP-ROM:esp32c3-20200918
Build:Sep 18 2020
rst:0xc (RTC_SW_CPU_RST),boot:0xc (SPI_FAST_FLASH_BOOT)
Saved PC:0x40381f4e
0x40381f4e: esp_restart_noos at /home/megumi/Mw/esp/esp-idf/components/esp32c3/system_api_esp32c3.c:130 (discriminator 1)

SPIWP:0xee
mode:DIO, clock div:1
load:0x3fcd6100,len:0x14
load:0x3fcd6114,len:0x1750
load:0x403ce000,len:0x878
load:0x403d0000,len:0x2bdc
entry 0x403ce000
I (59) boot: ESP-IDF v4.3-dev-2586-g526f68239 2nd stage bootloader
I (59) boot: compile time 21:59:04
I (59) boot: chip revision: 0
I (62) boot.esp32c3: SPI Speed      : 80MHz
I (67) boot.esp32c3: SPI Mode       : DIO
I (72) boot.esp32c3: SPI Flash Size : 2MB
I (77) boot: Enabling RNG early entropy source...
I (82) boot: Partition Table:
I (86) boot: ## Label            Usage          Type ST Offset   Length
I (93) boot:  0 nvs              WiFi data        01 02 00009000 00006000
I (100) boot:  1 phy_init         RF data          01 01 0000f000 00001000
I (108) boot:  2 factory          factory app      00 00 00010000 00100000
I (115) boot: End of partition table
I (120) esp_image: segment 0: paddr=00010020 vaddr=3c020020 size=04be0h ( 19424) map
I (131) esp_image: segment 1: paddr=00014c08 vaddr=3fc89470 size=01888h (  6280) load
I (138) esp_image: segment 2: paddr=00016498 vaddr=40380000 size=09464h ( 37988) load
I (152) esp_image: segment 3: paddr=0001f904 vaddr=00000000 size=00714h (  1812)
I (154) esp_image: segment 4: paddr=00020020 vaddr=42000020 size=13944h ( 80196) map
I (177) boot: Loaded app from partition at offset 0x10000
I (177) boot: Disabling RNG early entropy source...
I (188) cpu_start: Pro cpu up.
I (245) cpu_start: Pro cpu start user code
I (245) cpu_start: cpu freq: 160000000
I (245) cpu_start: Application information:
I (248) cpu_start: Project name:     hello-world
I (253) cpu_start: App version:      1
I (257) cpu_start: Compile time:     Apr 27 2021 21:58:56
I (264) cpu_start: ELF file SHA256:  0c9fd42d85362f6e...
I (270) cpu_start: ESP-IDF:          v4.3-dev-2586-g526f68239
I (276) heap_init: Initializing. RAM available for dynamic allocation:
I (283) heap_init: At 3FC8BB30 len 000344D0 (209 KiB): D/IRAM
I (290) heap_init: At 3FCC0000 len 0001F260 (124 KiB): STACK/DRAM
I (296) heap_init: At 50000000 len 00002000 (8 KiB): FAKEDRAM
I (303) spi_flash: detected chip: generic
I (308) spi_flash: flash io: dio
I (312) cpu_start: Starting scheduler.
Hello world!
This is esp32c3 chip with 1 CPU core(s), WiFi/BLE, silicon revision 0, 2MB external flash
Minimum free heap size: 331716 bytes
Restarting in 10 seconds...
Restarting in 9 seconds...
Restarting in 8 seconds...
```
這個是 idf4.3 編譯之後的輸出。當時還試了一下藍牙發現缺少頭文件，於是就算了。  
過了幾天之後咱找到個風水好一點的地方把 SDK 更新了一下。然後直接燒進去，然後可以看見
```
E (38) boot_comm: This chip is revision 2 but the application is configured for minimum revision 3. Can't run.
```
對的，咱買的是工程樣品，芯片的版本是2,這裏要改一下編譯設置。在 menuconfig ，找到 component config 然後進入 ESP32C3 specific 把 minimal version 改成2 就好了。  

下面是 hello_world 例程的輸出：
```
�ESP-ROM:esp32c3-20200918
Build:Sep 18 2020
rst:0x1 (POWERON),boot:0xc (SPI_FAST_FLASH_BOOT)
SPIWP:0xee
mode:DIO, clock div:1
load:0x3fcd6100,len:0x1654
load:0x403ce000,len:0x8b4
load:0x403d0000,len:0x2ba4
entry 0x403ce000
I (32) boot: ESP-IDF v4.4-dev-1183-g9d34a1cd4 2nd stage bootloader
I (32) boot: compile time 11:25:59
I (32) boot: chip revision: 2
I (35) boot.esp32c3: SPI Speed      : 80MHz
I (40) boot.esp32c3: SPI Mode       : DIO
I (45) boot.esp32c3: SPI Flash Size : 2MB
I (50) boot: Enabling RNG early entropy source...
I (55) boot: Partition Table:
I (59) boot: ## Label            Usage          Type ST Offset   Length
I (66) boot:  0 nvs              WiFi data        01 02 00009000 00006000
I (73) boot:  1 phy_init         RF data          01 01 0000f000 00001000
I (81) boot:  2 factory          factory app      00 00 00010000 00100000
I (88) boot: End of partition table
I (93) esp_image: segment 0: paddr=00010020 vaddr=3c020020 size=06440h ( 25664) map
I (105) esp_image: segment 1: paddr=00016468 vaddr=3fc89800 size=013d4h (  5076) load
I (110) esp_image: segment 2: paddr=00017844 vaddr=40380000 size=087d4h ( 34772) load
I (124) esp_image: segment 3: paddr=00020020 vaddr=42000020 size=1591ch ( 88348) map
I (140) esp_image: segment 4: paddr=00035944 vaddr=403887d4 size=00e5ch (  3676) load
I (141) esp_image: segment 5: paddr=000367a8 vaddr=50000000 size=00010h (    16) load
I (149) boot: Loaded app from partition at offset 0x10000
I (152) boot: Disabling RNG early entropy source...
I (169) cpu_start: Pro cpu up.
I (177) cpu_start: Pro cpu start user code
I (177) cpu_start: cpu freq: 160000000
I (177) cpu_start: Application information:
I (180) cpu_start: Project name:     hello-world
I (185) cpu_start: App version:      1
I (189) cpu_start: Compile time:     May  4 2021 11:25:56
I (195) cpu_start: ELF file SHA256:  8eae1b200ad945b9...
I (201) cpu_start: ESP-IDF:          v4.4-dev-1183-g9d34a1cd4
I (208) heap_init: Initializing. RAM available for dynamic allocation:
I (215) heap_init: At 3FC8BA40 len 000345C0 (209 KiB): DRAM
I (221) heap_init: At 3FCC0000 len 0001F260 (124 KiB): STACK/DRAM
I (228) heap_init: At 50000010 len 00001FF0 (7 KiB): RTCRAM
I (235) spi_flash: detected chip: generic
I (239) spi_flash: flash io: dio
I (243) sleep: Configure to isolate all GPIO pins in sleep state
I (250) sleep: Enable automatic switching of GPIO sleep configuration
I (257) cpu_start: Starting scheduler.
Hello world!
This is esp32c3 chip with 1 CPU core(s), WiFi/BLE, silicon revision 2, 2MB external flash
Minimum free heap size: 331876 bytes
Restarting in 10 seconds...
Restarting in 9 seconds...
Restarting in 8 seconds...
Restarting in 7 seconds...
Restarting in 6 seconds...
Restarting in 5 seconds...
Restarting in 4 seconds...
Restarting in 3 seconds...
Restarting in 2 seconds...
Restarting in 1 seconds...
Restarting in 0 seconds...
Restarting now.
```

下面是 hci/ble_adv_scan_combined 例程的輸出（MAC地址已經刪除）：
```
--- idf_monitor on /dev/ttyUSB0 115200 ---
--- Quit: Ctrl+] | Menu: Ctrl+T | Help: Ctrl+T followed by Ctrl+H ---
ESP-ROM:esp32c3-20200918
Build:Sep 18 2020
rst:0x1 (POWERON),boot:0xc (SPI_FAST_FLASH_BOOT)
SPIWP:0xee
mode:DIO, clock div:1
load:0x3fcd6100,len:0x1658
load:0x403ce000,len:0x8b4
load:0x403d0000,len:0x2ba4
entry 0x403ce000
I (32) boot: ESP-IDF v4.4-dev-1183-g9d34a1cd4-dirty 2nd stage bootloader
I (32) boot: compile time 23:10:04
I (32) boot: chip revision: 2
I (36) boot_comm: chip revision: 2, min. bootloader chip revision: 0
I (43) boot.esp32c3: SPI Speed      : 80MHz
I (48) boot.esp32c3: SPI Mode       : DIO
I (53) boot.esp32c3: SPI Flash Size : 2MB
I (57) boot: Enabling RNG early entropy source...
I (63) boot: Partition Table:
I (66) boot: ## Label            Usage          Type ST Offset   Length
I (74) boot:  0 nvs              WiFi data        01 02 00009000 00006000
I (81) boot:  1 phy_init         RF data          01 01 0000f000 00001000
I (88) boot:  2 factory          factory app      00 00 00010000 00100000
I (96) boot: End of partition table
I (100) boot_comm: chip revision: 2, min. application chip revision: 0
I (107) esp_image: segment 0: paddr=00010020 vaddr=3c040020 size=0ae70h ( 44656) map
I (123) esp_image: segment 1: paddr=0001ae98 vaddr=3fc8c800 size=02238h (  8760) load
I (126) esp_image: segment 2: paddr=0001d0d8 vaddr=40380000 size=02f40h ( 12096) load
I (135) esp_image: segment 3: paddr=00020020 vaddr=42000020 size=32464h (205924) map
I (173) esp_image: segment 4: paddr=0005248c vaddr=40382f40 size=0987ch ( 39036) load
I (181) esp_image: segment 5: paddr=0005bd10 vaddr=50000000 size=00010h (    16) load
I (184) boot: Loaded app from partition at offset 0x10000
I (185) boot: Disabling RNG early entropy source...
I (202) cpu_start: Pro cpu up.
I (210) cpu_start: Pro cpu start user code
I (210) cpu_start: cpu freq: 160000000
I (210) cpu_start: Application information:
I (213) cpu_start: Project name:     ble_adv_scan
I (218) cpu_start: App version:      1
I (223) cpu_start: Compile time:     Apr 29 2021 23:09:57
I (229) cpu_start: ELF file SHA256:  b486e59458a0b098...
I (235) cpu_start: ESP-IDF:          v4.4-dev-1183-g9d34a1cd4-dirty
I (242) heap_init: Initializing. RAM available for dynamic allocation:
I (249) heap_init: At 3FC8FC10 len 000303F0 (192 KiB): DRAM
I (255) heap_init: At 3FCC0000 len 0001F260 (124 KiB): STACK/DRAM
I (262) heap_init: At 50000010 len 00001FF0 (7 KiB): RTCRAM
I (268) spi_flash: detected chip: generic
I (273) spi_flash: flash io: dio
I (277) sleep: Configure to isolate all GPIO pins in sleep state
I (283) sleep: Enable automatic switching of GPIO sleep configuration
I (291) cpu_start: Starting scheduler.
W (305) BTDM_INIT: esp_bt_controller_mem_release not implemented, return OK
I (315) BTDM_INIT: BT controller compile version [6ab3130]
I (315) coexist: coexist rom version 8459080
I (315) phy_init: phy_version 500,985899c,Apr 19 2021,16:05:08
I (435) system_api: Base MAC address is not set
I (435) system_api: read default base MAC address from EFUSE
I (435) BTDM_INIT: Bluetooth MAC: 

I (445) BLE_ADV_SCAN: controller rcv pkt ready
I (445) BLE_ADV_SCAN: Event opcode 0x03 success.
I (455) BLE_ADV_SCAN: BLE Advertise, cmd_sent: 1
I (1455) BLE_ADV_SCAN: controller rcv pkt ready
I (1455) BLE_ADV_SCAN: Event opcode 0x01 success.
I (1455) BLE_ADV_SCAN: BLE Advertise, cmd_sent: 2
I (2455) BLE_ADV_SCAN: controller rcv pkt ready
I (2455) BLE_ADV_SCAN: Event opcode 0x06 success.
I (2455) BLE_ADV_SCAN: BLE Advertise, cmd_sent: 3
I (3455) BLE_ADV_SCAN: controller rcv pkt ready
I (3455) BLE_ADV_SCAN: Event opcode 0x08 success.
I (3455) BLE_ADV_SCAN: Starting BLE advertising with name "ESP-BLE-1"
I (3455) BLE_ADV_SCAN: BLE Advertise, cmd_sent: 4
I (4465) BLE_ADV_SCAN: controller rcv pkt ready
I (4465) BLE_ADV_SCAN: Event opcode 0x0a success.
I (4465) BLE_ADV_SCAN: BLE Advertising started..
I (4465) BLE_ADV_SCAN: BLE Advertise, cmd_sent: 5
I (5305) BLE_ADV_SCAN: Number of received advertising reports: 0
I (5475) BLE_ADV_SCAN: controller rcv pkt ready
I (5475) BLE_ADV_SCAN: Event opcode 0x0b success.
I (5475) BLE_ADV_SCAN: BLE Advertise, cmd_sent: 6
I (6475) BLE_ADV_SCAN: controller rcv pkt ready
I (6475) BLE_ADV_SCAN: Event opcode 0x0c success.
I (6475) BLE_ADV_SCAN: BLE Scanning started..
I (6475) BLE_ADV_SCAN: BLE Advertise, cmd_sent: 7
I (7475) BLE_ADV_SCAN: BLE Advertise, cmd_sent: 7
I (10305) BLE_ADV_SCAN: Number of received advertising reports: 2
******** Response 1/1 ********
Event type: 04
Address type: 00
Address:
Data length: 12
Advertisement Name: LYWSD03MMC
RSSI: -90dB
I (15305) BLE_ADV_SCAN: Number of received advertising reports: 5
******** Response 1/1 ********
Event type: 04
Address type: 00
Address: 
Data length: 12
Advertisement Name: LYWSD03MMC
RSSI: -88dB
```
## 0x03 總結
因爲沒啥時間弄，所以這篇主要是開箱，感覺還是挺好玩的，因爲多了一個藍牙，而且看起來很多可以用 esp8266 的地方都可以用這個，是個不錯的選擇。

