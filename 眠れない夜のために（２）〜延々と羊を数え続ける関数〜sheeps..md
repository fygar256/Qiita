


**************************************************


眠れない夜のために（２）〜延々と羊を数え続ける関数〜sheeps.


**************************************************



眠れない夜にこのプログラムを実行して、延々と画面を見続けましょう。
多倍長数なので、オーバーフローするまで寝ないで見続けると、多分、人間は死にます。

```
(defun sheeps () (do ((i 1 (+ i 1))) (() ()) (format t "羊が~A匹~%" i)))
```

clispのインストール（Ubuntu,Debian)

```
sudo apt-get install clisp
```
python版

```sheeps.py
#!/usr/bin/python3
print ("1 sheep.")
i=2
while True:
  print(i,"sheeps.")
  i+=1
```
