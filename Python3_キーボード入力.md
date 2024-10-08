


**************************************************


Python3 キーボード入力


**************************************************



Python3 で、キーボード入力があるまで待って、押されたキーを取得する。
簡単でかつよく使いそうな機能なのに、ネットを探してもなかなか見つからないので書いておきます。

何か画面出力をした後にキーボードが押されるのを待って、押されたらまた次の処理を始めたい場合とか、逐次的に、キーボード入力を見たいときってありますよね。
そういう時は、getkeyモジュールのgetkey関数を使いましょう。
しかし、あくまでこの関数は、Terminal入力を受け付けているときのみで、例えばpygameなどを使っていて、別のウィンドウがアクティブになっている時は使えません。

# インストール

```
$ python3 -m pip install getkey
```


# コード

```kb.py
#!/usr/bin/python3
from getkey import getkey,keys

print("Hit any key")

key=getkey() # wait key until keyboard is hit and input with no echoes

if key == keys.UP:
  print("Up key pressed.") # Handle the UP key
elif key == keys.DOWN:
  print("Down key pressed.") # Handle the DOWN key
elif key == keys.F1:
  print("F1 key pressed.") # Handle the F1 key
elif key == chr(10):
  print("Enter key pressed.") # Handle the Enter key
elif key == chr(27):
  print("Escape key pressed.") # Handle the ESCape key
else:
  print(key,"pressed.") # echo back

```
