


**************************************************


GAME言語でFibonacci数を求める


**************************************************



GAME言語でFibonacci数を求めるプログラムです。

```fibonacci.gm
#!/usr/bin/gamelinux
10*Fibonacci数を求める
100 "Input n of fibonacci(n):" N=?
110 A=0 B=1
120 ;=N=0 ?=A / #=-1
130 I=1,N-1 T=A A=B B=T+B @=I+1
140 ?=B /

```
