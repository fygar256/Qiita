


**************************************************


Ubuntu 18.04から20.04にアップグレードする時の注意点（リモートからのアップグレードは避けるべき）


**************************************************


Ubuntu 18.04から、20.04にアップグレードした時、デバイス周りがおかしくなって往生したときのことを話します。
<a href="https://qiita.com/hitobb/items/2ee9b1c2c49760976e0f"> Qiita:Ubuntu 18.04(LTS)→20.04(LTS)アップグレード方法</a>を参考にして、アップグレードしたのですが、この方法だと、直接ローカルマシンからアップグレードせずに、ssh経由でリモート端末からアップグレードをするために、xorgや、キーボード、サウンドなどの、デバイス周りがおかしくなって、CPU使用率も100％になってフリーズしていたので、設定し直すのに苦労をしました。
~~いろいろweb検索をして、試行錯誤をしてみても、pulseaudioがsnd-hda-intelのデバイスを拾わなくなったので、pulseaudioは/etc/pulse/default.paを書き換えて、何とか音を出しています。~~
2020年6月15日現在、pulseaudio周りのアップデートがあり、音は出ています。

CPU使用率が100％になって、フリーズしていたのは、`localectl set-keymap jp106`とすると直りました。やっぱり、直接ローカルマシンからアップグレードをするのが一番のようです。

ローカルマシンからアップグレードするのは以下の方法で。

#①パッケージを最新のものにしておく

```
sudo apt update
sudo apt upgrade
sudo apt autoremove
```

#②アップデートマネージャをインストールする

```
sudo apt-get install update-manager
```

#③アップグレード対象をLTSに書き換える

```
sudo vi /etc/update-manager/release-upgrades
```

で、

```
[DEFAULT]
    :
Prompt=lts
```

とする。

#④アップグレードを実行する。

```
sudo do-release-upgrade -d
```
