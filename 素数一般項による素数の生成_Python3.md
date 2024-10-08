


**************************************************


素数一般項による素数の生成 Python3


**************************************************


素数の一般項という数式を見つけたので、python3 で、mpmathモジュールを使って、任意精度演算で素数を求めるプログラムを書いてみました。
但し、一回の演算の回数が多すぎるため、小さい素数（２９とか）でも、遅くて、使い物にはなりません。
`chmod +x prime.py`として、実行権限を付けて動かして下さい。
Usage: prime.py

# 素数の一般項
説明などが欲しい方は、googleで、「素数　一般項」等として検索して下さい。

<img width=800 src="https://i.imgur.com/WOqzlPA.jpeg">

# 実行

mpmathモジュール は、
`$ python3 -m pip install mpmath`としてインストールして下さい。

ここでは、演算精度は200にしてあります。

# プログラム１

```prime.py
#!/usr/bin/python3
import mpmath
import sys
argv=sys.argv
n=int(argv[1])
mpmath.mp.dps = 200

s1=1
for m in range(1,int(mpmath.power(2,n)+1)):

 s2=0
 for k in range(1,m+1):
   s2+=mpmath.floor(mpmath.power(mpmath.cos((mpmath.factorial(k-1)+1)/k*mpmath.pi),2))

 s1=s1+mpmath.floor(mpmath.power(n/s2,1/n))
print(int(s1))
```

# プログラム２

こちらは、同じアルゴリズムで、次々と素数を表示していきます。

```prime4.py
#!/usr/bin/python3
import mpmath
import sys
argv=sys.argv
mpmath.mp.dps = 200

def nthprime(n):
  s1=1
  for m in range(1,int(mpmath.power(2,n)+1)):
    s2=0
    for k in range(1,m+1):
      s2+=mpmath.floor(mpmath.power(mpmath.cos((mpmath.factorial(k-1)+1)/k*mpmath.pi),2))
    s1=s1+mpmath.floor(mpmath.power(n/s2,1/n))
  return(s1)

for i in range(1,100):
  print(int(nthprime(i)))
```
