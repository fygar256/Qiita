


**************************************************


眠れない夜のために〜延々と２の冪乗を表示し続けるプログラム〜two〜


**************************************************


common lisp で書かれています。
ubuntuでの、CLISPのインストール方法は以下の通り。

```
sudo apt-get install clisp
```
TWO：コード

```
(defun two () (do ((i 1 (* i 2))) (() ()) (format t "~A ~%" i)))
```
python版

```two.py
#!/usr/bin/python3
i=1
while True:
  i=i*2
  print(i)
```
