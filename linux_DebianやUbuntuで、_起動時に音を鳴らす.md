


**************************************************


linux DebianやUbuntuで、 起動時に音を鳴らす


**************************************************


モニタレスで、サーバの遠隔操作をしていると、wake on lanで起動したのはいいものの、いつ起動完了になるかわからない。それなので、Debianで起動音を鳴らすことにした。

最初はbeepで鳴るかと思ってたら、beepでは音は出なかった。
ogg123で、oggファイルを再生することにした。aplayはうまく動かなかった。

```
apt-get install vorbis-tools
```
で、ogg123をインストール。

.config/autostart/ で指定する自動起動のシェルスクリプトで、下記の行を指定して下さい。
（自動起動のシェルスクリプトの作り方は、これを参照のこと：
 https://qiita.com/fygar256/items/020f685956df7d4fdb54 ）

```
ogg123 /usr/share/sounds/gnome/default/alerts/drip.ogg &
```
音が気に入らなければ、/usr/share/sounds/辺りで、適当なサウンドファイルを探して下さい。KDEが入っていれば、

```
/usr/share/sounds/KDE-Sys-App-Positive.ogg
```
辺りが良いでしょう。

これで、起動時に音が鳴るようになります。

