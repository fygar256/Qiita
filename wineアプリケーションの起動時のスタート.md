


**************************************************


wineアプリケーションの起動時のスタート


**************************************************


wineアプリケーションを起動時に自動スタートさせたい場合に方法が見つかりました。英語のページで見つけましたが、同じことを考えている人がいるもんです。

.lnkファイルからの実行はパスの表記の問題で、素直にアイコンのプロパティから.desktopファイルを作ってもうまく行きません。

ここでは、Google PhotoのAuto Backupを例に取ります。方法は以下の通り。

```
cd ~user
cp "~/.local/share/applications/wine/Programs/Google Photos Backup/Google Photos Backup.desktop" ~/.config/autostart
```
これで、起動時に自動的にアプリケーションが起動します。
