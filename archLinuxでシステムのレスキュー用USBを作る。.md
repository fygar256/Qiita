


**************************************************


archLinuxでシステムのレスキュー用USBを作る。


**************************************************



# 事の発端

レスキュー用LiveCDで、満足なものがなかったのが事の発端です。
SystemRescueCDというシステムを試してみたら、Archlinuxだったのですが、日本語環境がなく、他のLinux LiveCDでは不満だったためです。

### 基本archLinuxシステムのインストール

ここを参照してUSBにarchLinuxをインストールしてください。

https://qiita.com/fygar256/items/841d23c94161e9024ca5

### yayのインストール

```
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

### 各種ツールのインストール

```
sudo pacman -S gparted util-linux
yay -S partimage testdisk ddrescue
yay -S k3b
```

### Chromeのインストール

```
yay -S google-chrome
```
