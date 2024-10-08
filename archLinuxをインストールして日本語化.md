


**************************************************


archLinuxをインストールして日本語化


**************************************************



USBでもハードディスクでもSSDでも同様にしてインストールできます。
最近はarchinstallというものがあり、簡単にインストールできるようですが、うちではなぜか、archinstallが落ちます。


# 環境・準備するもの

/dev/sdbがドライブで、/dev/sdb1にルートパーティションがあるものとします。/bootと/は分けないものとします。
ユーザー名はhogeとします。ホスト名はfooとします。

DEもインストールします。
DEはmateにしました。
ブラウザはgoogle-chromeです。
display managerはlightdmです。
imはfcitx5です。

archLinuxのインストールメディアを用意して下さい。
ここでのストレージはUEFIではありません。MBR/BIOSです。

# 手順

・ディスクにfdiskでパーティションテーブルを作る。
・パーティションを切る

```
mkfs.ext4 /dev/sdb1
mount /dev/sdb1 /mnt
mkdir /mnt/home
pacstrap /mnt base base-devel linux linux-firmware
pacstrap /mnt man-db vim
arch-chroot /mnt
ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
hwclock --systohc
vim /etc/locale.gen # en_US.UTF-8とja_JP.UTF-8をアンコメント
locale-gen
echo "LANG=ja_JP.UTF-8" >/etc/locale.conf
echo "export LANG=C" >/root/.bash_profile
echo "KEYMAP=jp106" >/etc/vconsole.conf
echo "foo" > /etc/hostname
```

・/etc/hostsを以下のようにする。

```
127.0.0.1        localhost
::1              localhost
127.0.1.1        foo.localdomain    foo
```

```
passwd
pacman -S grub dosfstools os-prober mtools
grub-install /dev/sdb
pacman -S intel-ucode
mkdir /boot/grub
grub-mkconfig > /boot/grub/grub.cfg
pacman -S networkmanager dialog iw netctl
systemctl enable NetworkManager.service
reboot
```

・以上で、再起動してインストールしたシステムをブートする。

```
pacman -Syu
useradd -m -G wheel hoge
pacman -S sudo
EDITOR=vim visudo 
#%wheel ALL=(ALL) ALLをアンコメント
setxkbmap -layout jp -model pc106
timedatectl set-timezone Asia/Tokyo
```

### XとDEをインストールして日本語化

```
pacman -S xorg xorg-xinit
pacman -S mate mate-extra    # DEをcinnamonにする場合はpacman -S cinnamon
pacman -S lightdm lightdm-gtk-greeter
systemctl enable lightdm.service
pacman -S noto-fonts-cjk noto-fonts-emoji noto-fonts
pacman -S fcitx5-im fcitx5-mozc
pacman -S gnome-terminal
```

・/etc/environmentを以下のようにする。

```
GTK_IM_MODULE="fcitx5"
QT_IM_MODULE="fcitx5"
XMODIFIERS='@im=fcitx5'
```
・あとは、メニューの「設定」の中の「fcitx 設定」の、「システムキーボードのレイアウトを選択」で、「レイアウト」を「日本語」にする。

### ブラウザのインストール

```
su - hoge   # ユーザーhogeになる。
yay -S google-chrome-stable
```

# 終わり

まだ、swap領域など、他の設定も必要ですが、とりあえずのインストールはこれでいけます。
