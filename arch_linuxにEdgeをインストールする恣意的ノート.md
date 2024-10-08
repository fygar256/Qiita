


**************************************************


arch linuxにEdgeをインストールする恣意的ノート


**************************************************



### 本体のインストール
```
yay -S microsoft-edge-dev-bin
```

としてedge本体をインストール。

### addonのインストール

addon(拡張機能)として、
・auto tab discardをインストール
・Save & Repost Instagramをインストール
・twitter video downloaderをインストール
・Ad block　ultimateをインストール

youtubeや大概のサイトの動画のダウンロードは、yt-dlpでできます。

拡張機能のインストールは、メモリ節約のため、必要最小限にする。

### メモリの節約

Edgeを開いてGPUアクセラレーションを無効にします。

そして、/usr/share/applications/microsoft-edge-dev.desktopのExecのラインに --renderer-process-limit=3を付けてメモリの節約。
Edgeのインストール後、下記のコマンドを一回だけ実行して下さい。/usr/share/applications/にある.desktopファイルを書き換えます。

```
sudo sed /usr/share/applications/microsoft-edge-dev.desktop -e 's/Exec.*dev/Exec=\/usr\/bin\/microsoft-edge-dev --renderer-process-limit=3/' | sudo tee /usr/share/applications/microsoft-edge-dev.desktop >/dev/null
```
