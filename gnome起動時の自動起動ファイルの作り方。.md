


**************************************************


gnome起動時の自動起動ファイルの作り方。


**************************************************


実行させたいシェルスクリプトを、'mystartup'という名前でホームディレクトリに作り、実行権限を付けます。

そして、$HOME/.config/autostartに以下のファイルを作ります。

```mystartup.desktop
[Desktop Entry]
Type=Application
Version=1.0
Name=MYSTARTUP
Path=/home/user
Exec=mystartup
Icon=
Terminal=false
```
これで、gnome起動時に、ホームディレクトリの'mystartup'が自動的に実行されます。
名前や、ファイル名が気に入らなかったら、適当に変えて下さい。
