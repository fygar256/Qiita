


**************************************************


[Joke]　c0ffee python版


**************************************************



c0ffeeをpythonで書いてみました。

辞書ファイルから、16進数で表せる単語を探します。Oは0に置換します。

例えば、CAFE C0DE BEEF C0FFEE DEADBEEF等です。

`chmod +x c0ffee.py`として実行権を付け、`./c0ffee.py dictionaryfile.txt`として実行してください。
辞書ファイルは、TABかスペースか改行で区切られた英単語列です。

```c0ffee.py
#!/usr/bin/python
import sys

def hexword(w):
    w=w.replace('O','0').replace('o','0')
    for k in w:
        if k.upper() not in 'ABCDEF0':
            return ""
    return w

def main():
    fn=sys.argv[1]
    with open(fn,"rt") as file:
        text=file.read()
    words=text.split()
    words=[w for w in words if w]
    for i in words:
        if w:=hexword(i):
            print(w)

if __name__=="__main__":
    main()
```
