


**************************************************


Arch linuxにoperaのインストール。


**************************************************



ArchLinuxにOperaのインストールをします。

```
yay -S opera
```

そのままではtwitterやfacebookの動画が見れないので、
コーデックのインストールをします。

```
sudo pacman -S opera-ffmpeg-codecs
```

operaの日本語化は、実行時にオプション`--lang=ja`を付けるだけでOk
