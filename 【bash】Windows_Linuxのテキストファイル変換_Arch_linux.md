


**************************************************


【bash】Windows Linuxのテキストファイル変換 Arch linux


**************************************************



日本語テキストファイル。WindowsとLinuxの相互テキストファイル変換の事。

# 環境

Arch linuxです。

# 方法

(1)　文字コード変換   はnkfがやります。　
(2)　改行コード変換   は、/usr/bin/dos2unixと/usr/bin/unix2dosがやります。DOSはCR+LFが改行コードで、UNIXはLFです。

# ツールのインストール

```
yay -S nkf-git dos2unix
```

# コマンド化

.bashrcに関数を作って、面倒なタイプを減らしましょう。
.bashrcに書き加えるコードを載せておきます。.bashrcを書き換えた後は、`source ~/.bashrc`で更新しましょう。

```
function dos2unix() {
    mv $1 $1.save
    nkf --ic=CP932 --oc=UTF8 $1.save | /usr/bin/dos2unix >$1
}

function unix2dos() {
    mv $1 $1.save
    nkf --ic=UTF8 --oc=CP932 $1.save | /usr/bin/unix2dos >$1
}
```

これで、Windowsのテキストファイルから、Linuxのテキストファイルに変換する時は、`dos2unix file`、逆は`unix2dos file`でいけます。 元のファイルはfile.saveという名前で残ります。dos2unixとunix2dosは名前を再定義していますが、この方がやりやすいと思ったので。

# 注意

・^Zが見つかった時はこういうメッセージが出ますが、ちゃんと変換されています。

```
dos2unix: バイナリ文字 0x1A が 20 行目で見つかりました
dos2unix: バイナリファイル stdin をスキップしています
```

