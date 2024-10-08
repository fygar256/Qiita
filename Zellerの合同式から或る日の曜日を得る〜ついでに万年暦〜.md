


**************************************************


Zellerの合同式から或る日の曜日を得る〜ついでに万年暦〜


**************************************************


Zellerの合同式から、ある日の曜日が何曜日であるかを算出します。

```calcal.py
#!/usr/bin/python3
import sys
y = int(sys.argv[1])
m = int(sys.argv[2])
d = int(sys.argv[3])
youbia = ["日", "月", "火", "水", "木", "金", "土"]

if (m == 1 or m == 2):
    md = m+10
    yd = y-1
else:
    md = m-2
    yd = y

a = yd // 100
b = yd % 100
youbi = (int(2.6*md - 0.2) + d + b + b//4 + a//4 - 2*a)%7

print("西暦", y, "年", m, "月", d, "日は", youbia[youbi], "曜日です。",sep="")

```

出力例

```
$ calcal.py 1967 5 12
西暦1967年5月12日は金曜日です。
$ calcal.py 1994 12 25
西暦1994年12月25日は日曜日です。
$ calcal.py 1995 12 24
西暦1995年12月24日は日曜日です。
$ calcal.py 1998 12 24
西暦1998年12月24日は木曜日です。
$ calcal.py 2000 5 12
西暦2000年5月12日は金曜日です。
$ calcal.py 2019 12 25
西暦2019年12月25日は水曜日です。
$ calcal.py 10000 5 12
西暦10000年5月12日は金曜日です。
$ 
```


#万年暦
cal.py version 2.0
曜日算出と万年暦を纏めたコードを示します。曜日算出関数は、kkddさんの提示してくれた整数演算のものです。
$ ./cal.py y m で、西暦y年m月のカレンダーを表示します。
$ ./cal.py y m dで、西暦y年m月d日の曜日を表示します。
天文学的紀年法で、yは0や負数も指定できます。
BC1年はcal.pyの西暦0年、BC2年はcal.pyの西暦-1年に相当します。

```cal.py
#!/usr/bin/python3
import sys
argvs = sys.argv
argc = len( argvs )
months=[31,28,31,30,31,30,31,31,30,31,30,31]
youbia = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"]

def dayofweek(year, month, day):
  m = (month-3)%12
  y = year if month >= 3 else year - 1
  n = day + (13*m+2)//5 + y + y//4 - (y//100) + y//400 + 2
  return (n%7)

def printcal(y,m):
  mm=months[m-1]+(1 if (m==2 and (y%4 == 0 and y%100 != 0 or y%400 == 0)) else 0)
  print(y,m,sep='/')
  print (*youbia,sep=' ')
  f=dayofweek(y,m,1)
  print("    "*f,end='')
  for i in range(1,mm+1):
    print(" %2d " %i,end='')
    if f==6:
      print("")
    f=(f+1)%7
  if f!=0:
    print("")

if argc==3:
  y = int(sys.argv[1])
  m = int(sys.argv[2])
  printcal(y,m)
elif argc==4:
  y = int(sys.argv[1])
  m = int(sys.argv[2])
  d = int(sys.argv[3])
  f = dayofweek(y,m,d)
  print(y, "/", m, "/", d, " is ", youbia[f],sep="")

else:
  print("Usage : cal.py y m [d]")
  exit(1)

```

# 出力例

```
$ cal.py 2020 2
2020/2
Sun Mon Tue Wed Thu Fri Sat
                          1 
  2   3   4   5   6   7   8 
  9  10  11  12  13  14  15 
 16  17  18  19  20  21  22 
 23  24  25  26  27  28  29 
$
```

 
