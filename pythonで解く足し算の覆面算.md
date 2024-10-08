


**************************************************


pythonで解く足し算の覆面算


**************************************************


足し算の覆面算を解きます。可能な解を全て出力します。総当りのアルゴリズムではありません。

pythonで書かれていますが、もっと良いコーディングがあると思います。
アルゴリズムは、奥村晴彦著、「C言語による最新アルゴリズム事典」に依ります。

コードに実行権限を付け、例えば、`./fukumen.py SEND MORE MONEY`のように、コマンドラインに覆面算の各文字列を指定して実行して下さい。

```fukumen.py
#!/usr/bin/python3
import sys

N = 128
s = "0123456789ABCDEFGHIJKLMNOPQRTSUVWXYZ"
word = [['' for i in range(N)] for j in range(128)]
digit = [0 for i in range(len(s))]
l = [0 for i in range(len(s))]
ok = [False for i in range(10)]
ii = 0
jj = 0
carry = 0
solution = 0


def found():
    global solution
    solution += 1
    print("\n解 %d" % solution)
    for i in range(imax):
        if (i == imax-1):
            print("-"*jmax)
        for j in range(jmax):
            k = jmax-1-j
            c = word[i][k]
            if (c == ''):
                print(" ", end="")
            else:
                print("%d" % digit[s.index(c)], end='')
        print("")


def tr(sum):
    global ii, jj
    w = word[ii][jj]
    c = 0 if w == '' else s.index(w)
    if (ii < imax-1):
        ii += 1
        d = digit[c]
        if (d < 0):
            d = l[c]
            while(d <= 9):
                if (ok[d]):
                    digit[c] = d
                    ok[d] = False
                    tr(sum+d)
                    ok[d] = True
                d += 1
            digit[c] = -1
        else:
            tr(sum+d)
        ii -= 1
    else:
        jj += 1
        ii = 0
        carry, d = divmod(sum, 10)
        if (digit[c] == d):
            if (jj < jmax):
                tr(carry)
            elif (carry == 0):
                found()
        else:
            if (digit[c] < 0 and ok[d] and d >= l[c]):
                digit[c] = d
                ok[d] = False
                if (jj < jmax):
                    tr(carry)
                elif (carry == 0):
                    found()
                digit[c] = -1
                ok[d] = True
        jj -= 1
        ii = imax-1


argv = sys.argv
argv.pop(0)
imax = len(argv)
jmax = max(map(len, argv))

for i in range(imax):
    argv[i] = argv[i].upper()
    l[s.index(argv[i][0])] = 1
    a = argv[i][-1::-1]
    for j in range(len(a)):
        word[i][j] = a[j]
        c = word[i][j]
        if (c.isalpha()):
            digit[s.index(c)] = -1
        elif (c.isdigit()):
            digit[s.index(c)] = int(c)
        else:
            print("Invalid parameter.")
            exit(1)

for i in range(10):
    ok[i] = True
tr(0)
if (solution == 0):
    print("解はありません。")
exit(0)

```

#実行例

```
$ ./fukumen.py send more money

解 1
 9567
 1085
-----
10652



$ ./fukumen.py five seven eleven twelve fifteen twenty seventy

解 1
   3209
  59094
 969094
 819609
3238994
 819487
-------
5909487


```

