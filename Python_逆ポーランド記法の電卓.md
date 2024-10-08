


**************************************************


Python 逆ポーランド記法の電卓


**************************************************



逆ポーランド記法（Reverse Polish Notation;RPN)の電卓です。
chmod +x revp.pyで実行権を付けて動かして下さい。
引数の式はコマンドラインに、`revp.py 10 20 +`のように、スペースで区切って入力して下さい。
エラーチェックはしてませんので、誤った文字列を渡すと、pythonがエラーを返します。

```revp.py
#!/usr/bin/python3
import sys
stack=[]
s=sys.argv[1:]

while(s):
  e=s[0]
  if (e=='+'):
    stack.append(stack.pop()+stack.pop())
  elif (e=='-'):
    a=stack.pop()
    stack.append(stack.pop()-a)
  elif (e=='*'):
    stack.append(stack.pop()*stack.pop())
  elif (e=='/'):
    a=stack.pop()
    stack.append(stack.pop()/a)
  else:
    stack.append(float(e))
  s=s[1:]

print(stack.pop())

```

実行例

```
$ revp.py 10 20 +
30.0
$ revp.py 10 20 /
0.5
$ revp.py 10 20 - 30 +
20.0
$ revp.py 10 20 30 + -
-40.0
```
