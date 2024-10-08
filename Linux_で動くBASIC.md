


**************************************************


Linux で動くBASIC


**************************************************


Linuxで動くBASICインタプリタ、bwbasicを試してみました。
Linuxで動くBASICインタプリタは、色々あって、１０進BASIC何ていうのもありますが、
数値計算用ではなく、ちょっと動かして見ただけなので、bwbasicを選択しました。
インストール：
sudo apt-get install bwbasic

実行例

```
$ bwbasic
Bywater BASIC Interpreter/Shell, version 2.20 patch level 2
Copyright (c) 1993, Ted A. Campbell
Copyright (c) 1995-1997, Jon B. Volkoff
 
bwBASIC: 
bwBASIC: 10 for i=1 to 10
bwBASIC: 20 print "Good Morning. God Bless Us All."
bwBASIC  30 next
bwBASIC:
bwBASIC: list
     10: for i=1 to 10
     20: print "Good Morning. God Us All."
     30: next
bwBASIC: run
Good Morning. God Bless Us All.
Good Morning. God Bless Us All.
Good Morning. God Bless Us All.
Good Morning. God Bless Us All.
Good Morning. God Bless Us All.
Good Morning. God Bless Us All.
Good Morning. God Bless Us All.
Good Morning. God Bless Us All.
Good Morning. God Bless Us All.
Good Morning. God Bless Us All.
bwBASIC: 
```
