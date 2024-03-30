---
title: "I3wm 配置(僞)"
date: 2019-08-07T21:56:00+08:00
description: "emmm，不就是個複製粘貼的事情嗎？"
topics:
  - 新手教程
tags:
  - i3wm
draft:  false
---
之前提過了 KDE 但實際上咱平時用的是 i3wm，但就像名字說的那樣這個就是個 wm ，窗口管理器，所以別期望 i3 給汝全套的服務。說實話咱也不知道要怎麼寫，那就隨便寫寫吧。以下內容以基本上爲解釋咱的配置文件，汝如果想得到和咱一樣的效果直接複製就好了，不過咱還是建議汝康康不要直接複製。~~其實這篇東西是用來水的，因爲暫時不知道些什麼，emmm，這不就是複製粘貼p配置文件的事情嗎？有什麼好寫的（摔~~

咱的配置文件在 [這裏](https://github.com/MarvelousBlack/Vanilla/blob/Vanilla/i3/.config/i3/config) 汝只需要對照修改汝的 `~/.config/i3/config` 就好了。下面開始解釋這份文件。
```
exec --no-startup-id /usr/lib/polkit-kde-authentication-agent-1
exec --no-startup-id fcitx
exec --no-startup-id udiskie -snA&
exec --no-startup-id sleep 3 && exec feh --bg-fill background_desktop.png background_desktop.jpg
exec --no-startup-id compton -CGb --backend glx --paint-on-overlay --glx-no-stencil  --unredir-if-possible
exec --no-startup-id termite
exec --no-startup-id kdeconnect-indicator&
exec --no-startup-id klipper&
exec --no-startup-id xss-lock -- i3lock -n -t --ignore-empty-password --no-unlock-indicator  -i background.png &
```
這一段是啓動項，前文說了 i3 只是一個窗口管理器，有很多東西都需要汝自己去配置，這裏從上倒下依次爲啓動 kde 的 polkit
驗證的那個東西，也就是在部分需要提權的地方跳出來讓汝輸入密碼的那個框。fcitx 是輸入法， udiskie 是自動掛載 u 盤的小程序，feh 是用來設置壁紙的（爲了方便咱直接吧圖片命名爲 `background_desktop.png` 和 `background_desktop.jpg` 並存於家目錄下如果使用的是咱的這份文件，那麼汝需要將汝自己的圖片放到家目錄下並重命名爲這兩個。）compton 混成器，程序的透明等都是他完成的，termite 那個是個終端，咱想開機就直接開個終端，klipper 是剪貼板工具，最後那個是設置 i3lock 爲鎖屏。  
設置焦點跟隨光標
```
focus_follows_mouse yes
```
設置 mod 鍵爲 super 鍵 就是那個常說 win 鍵。  
```
set $mod Mod4
```

下面是設置截圖使用 import 這個工具和 scrot 來截圖，快捷鍵是 Print 和 `$mod+Print`
```
# Take a screenshot upon pressing $mod+print (select an area)
bindsym --release Print exec --no-startup-id "import /home/marvelous/latest-screenshot.png && xclip -selection clipoard -t 'image/png' /home/marvelous/latest-screenshot.png"
bindsym $mod+Print exec scrot

```
設置窗口邊框字體
```
font pango:Droid Sans Mono 7.3
```
下面就是綁定鍵打開對應的程序了，就不一一寫出來了
```
bindsym XF86HomePage exec chromium
bindsym $mod+Return exec termite
bindsym $mod+t exec thunar
```

按下 `$mod+m` 窗口透明
```
bindsym --release $mod+m exec transset-df 0.8
```
設置按住 mod 鍵可以拖動窗口移動和改變大小
```
floating_modifier $mod
```
使用 `$mod+d` 呼出應用啓動發射器（咱用的是 j4 ）
```
bindsym $mod+d exec --no-startup-id j4-dmenu-desktop --dmenu="dmenu -i -fn 'Droid Sans Mono-9'" --term="xfce4-terminal" --display-binary
```
接着就是改變窗口的焦點和移動窗口用的
```
# change focus
bindsym $mod+h focus left
bindsym $mod+j focus down
bindsym $mod+k focus up
bindsym $mod+l focus right

# alternatively, you can use the cursor keys:
bindsym $mod+Left focus left
bindsym $mod+Down focus down
bindsym $mod+Up focus up
bindsym $mod+Right focus right

# change focus between tiling / floating windows
bindsym $mod+space focus mode_toggle

# focus the parent container
bindsym $mod+a focus parent
bindsym $mod+x [urgent=latest] focus


# move focused window
bindsym $mod+Shift+h move left
bindsym $mod+Shift+j move down
bindsym $mod+Shift+k move up
bindsym $mod+Shift+l move right

# alternatively, you can use the cursor keys:
bindsym $mod+Shift+Left move left
bindsym $mod+Shift+Down move down
bindsym $mod+Shift+Up move up
bindsym $mod+Shift+Right move right
```
下一次窗口出現所使用的分屏方式 mod+g 是左右分，mod+v 是上下分
```
bindsym $mod+g split h
bindsym $mod+v split v
```
全屏的和容器樣式轉換
```
# enter fullscreen mode for the focused container
bindsym $mod+f fullscreen toggle

# change container layout (stacked, tabbed, toggle split)
bindsym $mod+s layout stacking
bindsym $mod+w layout tabbed
bindsym $mod+e layout toggle split
```
開關焦點窗口是否懸浮
```
# toggle tiling / floating
bindsym $mod+Shift+space floating toggle
```
隱藏和顯示窗口
```
bindsym $mod+minus move scratchpad
bindsym $mod+plus scratchpad show
```
改變邊框
```
bindsym $mod+u border none
bindsym $mod+n border normal
bindsym $mod+o border 1pixel
bindsym $mod+b border toggle
```
修改 workspace 名稱，並綁定到對應的顯示器（咱這裏寫死了，汝需要根據汝的需要更改）
```
# Workspace names
workspace "11" output DP-1
workspace "12" output DP-1
workspace "13" output DP-1
workspace "1" output eDP-1-1
```
切換 workspace 
```
# switch to near workspace
bindsym $mod+Tab workspace next
bindsym mod1+Tab workspace prev
# switch to workspace
bindsym $mod+1 workspace 1
bindsym $mod+2 workspace 2
bindsym $mod+3 workspace 3
bindsym $mod+4 workspace 4
bindsym $mod+5 workspace 5
bindsym $mod+6 workspace 6
bindsym $mod+7 workspace 7
bindsym $mod+8 workspace 8
bindsym $mod+9 workspace 9
bindsym $mod+0 workspace 10
bindsym $mod+F1 workspace 11
bindsym $mod+F2 workspace 12
bindsym $mod+F3 workspace 13
bindsym $mod+F4 workspace 14
bindsym $mod+F5 workspace 15
bindsym $mod+F6 workspace 16
bindsym $mod+F7 workspace 17
bindsym $mod+F8 workspace 18
bindsym $mod+F9 workspace 19
bindsym $mod+F10 workspace 20
```
移動焦點到對應的 workspace 

```
# move focused container to workspace
bindsym $mod+Shift+1 move container to workspace 1
bindsym $mod+Shift+2 move container to workspace 2
bindsym $mod+Shift+3 move container to workspace 3
bindsym $mod+Shift+4 move container to workspace 4
bindsym $mod+Shift+5 move container to workspace 5
bindsym $mod+Shift+6 move container to workspace 6
bindsym $mod+Shift+7 move container to workspace 7
bindsym $mod+Shift+8 move container to workspace 8
bindsym $mod+Shift+9 move container to workspace 9
bindsym $mod+Shift+0 move container to workspace 10
bindsym $mod+Shift+F1 move container to workspace 11
bindsym $mod+Shift+F2 move container to workspace 12
bindsym $mod+Shift+F3 move container to workspace 13
bindsym $mod+Shift+F4 move container to workspace 14
bindsym $mod+Shift+F5 move container to workspace 15
bindsym $mod+Shift+F6 move container to workspace 16
bindsym $mod+Shift+F7 move container to workspace 17
bindsym $mod+Shift+F8 move container to workspace 18
bindsym $mod+Shift+F9 move container to workspace 19
bindsym $mod+Shift+F10 move container to workspace 20

```
設置重新載入配置和i3用的按鍵
```
bindsym $mod+Shift+c reload
bindsym $mod+Shift+r restart
```
改變窗口大小
```
# resize window (you can also use the mouse for that)
mode "resize" {
        # These bindings trigger as soon as you enter the resize mode

        # Pressing left will shrink the window’s width.
        # Pressing right will grow the window’s width.
        # Pressing up will shrink the window’s height.
        # Pressing down will grow the window’s height.
        bindsym h resize shrink width 5 px or 5 ppt
        bindsym j resize grow height 5 px or 5 ppt
        bindsym k resize shrink height 5 px or 5 ppt
        bindsym l resize grow width 5 px or 5 ppt

        # same bindings, but for the arrow keys and 10
        bindsym Left resize shrink width 10 px or 10 ppt
        bindsym Down resize grow height 10 px or 10 ppt
        bindsym Up resize shrink height 10 px or 10 ppt
        bindsym Right resize grow width 10 px or 10 ppt

        # back to normal: Enter or Escape
        bindsym Return mode "default"
        bindsym Escape mode "default"
}
bindsym $mod+r mode "resize"u
```
設置是否顯示 i3bar
```

# Start i3bar to display a workspace bar (plus the system information i3status
# i3bar toggle
bindsym $mod+i bar mode toggle
# finds out, if available)
```
設置 bar 的顏色等，和使用的命令等等（咱這裏的用一個改過的腳本來顯示bar的，在上面的鏈接中可以找到）
```
bar {

   status_command ~/.config/i3/net-speed.sh
   i3bar_command i3bar -t
   position top
   tray_output primary
	colors {
		statusline #000000
		background #F4ADD96B
		separator #2D2D2D
		focused_workspace #636e88 #285de7 #dedfdg
		active_workspace #556677 #234567 #56ef67
		inactive_workspace #636d72 #2d2d2d #dedede
		urgent_workspace #ffffff #900000 #d23d32
		}

}
```
設置窗口樣式，顏色之類的。
```
#---window style---

# new window
new_window none
new_float normal
hide_edge_borders both

# window colors
#  class                 border   backgr.  text  indicator  child_border
client.focused          #4c7899  #285577  #ffffff  #2e9ef4   #285577
client.focused_inactive #81c2d6  #5f676a  #ffffff  #484e50   #0b6e48
client.unfocused        #c9cabb  #222222  #888888  #292d2e   #222222
client.urgent           #2f343a  #900000  #ffffff  #199475   #900000
client.placeholder      #a2b4ba  #0c0c0c  #ffffff  #1793d0   #0c0c0c
client.background       #82abba
```
設置彈出和讓部分軟體在啓動的時候就是懸浮狀態
```
# popups
for_window [window_role="pop-up"] floating enable
for_window [window_role="task_dialog"] floating enable

# float programs(find the programs'names in "/usr/share/applictions")
for_window [class="Gpicview"] floating enable
for_window [class="mpv"] floating enable
for_window [class="Gimp"] floating enable
for_window [class="Xarchiver"] floating enable
for_window [class="Genymotion"] floating enable
for_window [class="VirtualBox"] floating enable
for_window [class="shadowsocks-qt5"] floating enable
for_window [class="netease-cloud-music"] floating enable
```
固定部分軟體出現的容器
```
assign [class="VirtualBox"] 10
```
音量控制
```
#===volume control===
bindsym XF86AudioRaiseVolume exec --no-startup-id amixer  set Master 5%+ unmute
bindsym XF86AudioLowerVolume exec --no-startup-id amixer  set Master 5%- unmute
bindsym XF86AudioMute exec --no-startup-id amixer set Master toggle
#& amixer set Headphone toggle &amixer set Speaker toggle
```
背光控制
```
#===balcklight control==
bindsym $mod+XF86AudioRaiseVolume exec xbacklight +10
bindsym $mod+XF86AudioLowerVolume exec xbacklight -10
bindsym XF86MonBrightnessUp exec xbacklight +10
bindsym XF86MonBrightnessDown exec xbacklight -10
```
電源開關機按鈕
```
#===power manager===
set $mode_system select: lock(L) exit(E) reboot(R) poweroff(O) suspend(S) hybrid-sleep(Y) hibernate(H) cancel(Esc)
bindsym $mod+Shift+e mode "$mode_system"
mode "$mode_system" {
    bindsym l exec --no-startup-id i3lock -n -t --ignore-empty-password --no-unlock-indicator  -i background.png  , mode "default"
   bindsym e exec --no-startup-id i3-msg exit, mode "default"
   bindsym s exec --no-startup-id systemctl suspend, mode "default"
   bindsym y exec --no-startup-id systemctl hybrid-sleep, mode "default"
   bindsym h exec --no-startup-id systemctl hibernate, mode "default"
   bindsym r exec --no-startup-id systemctl reboot, mode "default"
   bindsym o exec --no-startup-id systemctl poweroff, mode "default"
    bindsym Escape mode "default"
}
```
大概隨便的寫解釋了一下，說是美化整個文件看下來，汝可以動的只有顏色是否有窗邊框等等，沒有多少是可以變動的。咱並不在乎是不是好看，好用就可以了，所以就這樣隨便的用着了。
