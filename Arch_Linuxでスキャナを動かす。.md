


**************************************************


Arch Linuxでスキャナを動かす。


**************************************************




ArchLinuxでスキャナが動いた。モデルはブラザーのDCP-J952Nです。ネットワーク接続で動かします。

インストール

```
sudo pacman -S xsane
yay -S brscan4
```

設定

```
brsaneconfig4 -a name=DCP-J952N model=DCP-J952N ip=192.168.003.002
```

ipaddressは固定です。

確認

```
$ scanimage --list-devices
device `brother4:net1;dev0' is a Brother DCP-J952N DCP-J952N
$ 
```

あとは、xsaneを呼び出せば、スキャンできます。
