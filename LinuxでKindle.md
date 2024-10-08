


**************************************************


LinuxでKindle


**************************************************


Amazon Kindle for pcをUbuntuで動かそうとして、wineをいじっていたのだが、
プロキシがなんたらかんたらとか言うエラーが出て動かなかった。
そこで、苦心惨憺のweb検索の結果、解決方法を見つけた。

# 環境
wine6.0.3 (32bit)
ubuntu22.04LTS
Kindle 1.26.0
wineのwindowsのバージョンはWindows 10

# 問題点と解決策


### (1)プロキシエラーの回避
$ sudo dpkg-reconfigure ca-certificates
をして、
mozilla/VeriSign_Class_3_Public_Primary_Certification_Authority
を有効にすると、プロキシエラーが消えた。

### (2)Kindleが実行時エラーを出して止まる

$ mkdir -p /home/USER/.wine/drive_c/users/USER/AppData/Local/Amazon/Kindle
もしておかなければならない。これをやらないとKindleがエラーで落ちる。

### (3)ログイン画面が真っ白（Geckoが動いてない）


Geckoのインストールは、コマンドラインから、
$ wine iexplore
をして、一旦Internet Explorerを起動して、Geckoをリストアする。

# 終わりに

64ビットwineではまだwine自体に不具合が多くて動かしてない。うちのは基本的に32bit wineなもので。

# 追記

KindleはArch Linuxで、特に何もしないでwine(32bit)で動きました。
