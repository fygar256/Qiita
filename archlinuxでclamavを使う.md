


**************************************************


archlinuxでclamavを使う


**************************************************


Linuxを使っていますが、chromeで、ウェブを閲覧しているとウィルスにやられました。clamavというアンチウィルスソフトを使ってみようと思います。

### インストール

```
sudo pacman -S clamav
```

### ウィルス・データベースのアップデート

```
sudo freshclam
```

### 準備

感染ファイルを隔離するディレクトリINFECTEDを作っておきます。

```
sudo mkdir /root/INFECTED
```


### 実行

テストです。このままでも実行可能です。
トップディレクトリを/にとり、ログファイルを/root/clamscan.logに出力し、感染したファイルを/root/INFECTEDに移動させます。

```
su
<パスワード入力>
cd ~
clamscan -i -v --log=/root/clamscan.log --move=/root/INFECTED -r / &
```

### 一般ユーザーからコマンド一発で実行できるように.bashrcを書き換える

.bashrcに下記を追加します。
nohupでバックグラウンドジョブにし、niceでジョブの優先順位を最低にし、ログファイルを/root/clamscan.logに出力し、感染ファイルを/root/INFECTEDに移動するようにし、第一引数をトップディレクトリにとり、nohupの標準出力と標準エラー出力を/root/clam.outにまとめて出力するようにします。

```
function clamscan {
    sudo sh -c "nohup nice -20 clamscan -i -v --log=/root/clamscan.log --move=/root/INFECTED -r '$1' >/root/clam.out 2>&1 &"
}
```

追加したあとは
```
source .bashrc
```
で更新しておきましょう。

### 実行

```
$ clamscan $HOME
```

ウェブ閲覧に感染したウィルスなので、ホームディレクトリ以下に感染してると思い、$HOMEからスキャンします。

### コメント

clamavでは引っかからなかったため、adblockerを変えたり、`rm -rf .config/google-chrome`をしたりしましたが、結局chromeの「設定をリセットする」で駆除できたようです。
