


**************************************************


FreeBSD インストールノート


**************************************************



## 基本システムのインストール

まず、FreeBSDのインストールメディアを作り、基本システムをインストールします。英語なので少し読みにくいのですが、FreeBSDはシステムがきれいなので、インストールはやりやすいでしょう。僕のマシンは古いマシンで、ディスクの容量も小さいので、ファイルシステムはUFSでインストールしました。

#### sudo,suを許可

userをwheelグループに追加します。

```
# visudo
%wheel ALL=(ALL:ALL) ALL # コメントアウトを外す
# vi /etc/group
wheel:*:0:root,user      # userをwheelグループに追加
```

## xorg、Desktop Environmentのインストール。

#### xorgのインストール

グラフィカルインターフェースのために、xorgをインストールします。

```
# pkg install xorg
```

```/usr/local/etc/X11/xorg.conf
Section    "Module"
    Load   "freetype"
EndSection
```

```/usr/local/etc/X11/xorg.conf.d/input.conf
Section    "Files"
    FontPath    "/usr/local/share/fonts/dejavu"
EndSection
Section "InputClass"
        Identifier      "libinput keyboard catchall"
        MatchIsKeyboard "on"
        MatchDevicePath "/dev/input/event*"
        Driver          "libinput"
        Option          "XkbRules" "evdev"
        Option          "XkbModel" "jp106"
        Option          "XkbLayout" "jp"
EndSection
```
#### DEのインストール

DEはxfce,display managerはlightdmにしました。

```
# pkg install xfce xfce4-goodies lightdm lightdm-gtk-greeter
```

```/etc/rc.confに追加
dbus_enable="YES"
lightdm_enable="YES"
```
これで、次回起動したらlightdmが動き、ログイン画面になります。

#### 日本語環境

```$HOME/.xprofile
#!/bin/sh
export LANG=ja_JP.UTF-8
export LC_ALL=ja_JP.UTF-8
export LC_CTYPE=ja_JP.UTF-8
 
export DefaultIMModule=fcitx
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
export XMODIFIER=@im=fcitx
/usr/local/bin/mozc start
fcitx -r -d
````

```
# pkg install ja-font-std ja-font-vlgothic ja-font-takao
# pkg install ja-fcitx-mozc ja-mozc-server ja-mozc-tool zh-fcitx zh-fcitx-configtool
```

#### ブラウザのインストール

```
# pkg install firefox
```
