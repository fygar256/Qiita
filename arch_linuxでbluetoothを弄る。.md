


**************************************************


arch linuxでbluetoothを弄る。


**************************************************


archlinuxで、bluetooth経由の携帯電話からのファイル転送がうまく行かなかったので、何でかと思っていたら、bluetoothのグループとユーザーを登録する必要があった。

まずは、bluezパッケージのインストール

```
sudo pacman -S bluez bluez-utils
```

そして、グループとユーザーを登録。ここでは、garというユーザー。

```
sudo groupadd -g 1002 bluetooth       # グループbluetoothをgid 1002で作成
sudo usermod -aG bluetooth gar        # グループbluetoothにユーザーgarを追加
sudo systemctl restart bluetooth      # bluetoothサービスのリスタート
```

うまく行った。
