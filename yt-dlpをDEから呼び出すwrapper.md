


**************************************************


yt-dlpをDEから呼び出すwrapper


**************************************************


yt-dlpをDE（MATEやcinnamonやxfce4やLXDE）から呼び出すwrapperです。
どうしても、ytdlp-guiが動かなかったので作りました。
DEにランチャを作り、このプログラムを「端末内で実行」するようにしておけば、GUIからyt-dlpが呼び出せます。

### yt-dlpのインストール

```
yay -S yt-dlp
```

yt-dlpは、URLさえ解れば、ウェブ上の大概の動画とFacebookの動画と、Twitterの一部の動画をダウンロードできます。

### wrapper

`chmod +x videodownloader.py`として、実行権を付けておいて下さい。

```videodownloader.py
#!/usr/bin/env python3
import subprocess

savedir="/home/user/Videos"
print("Youtube Downloader")
url=input("Input URL:")
subprocess.call(["/usr/bin/yt-dlp","-P",savedir,"--trim-filenames","32",url])
print("Done. Hit enter key")
a=input()
```
