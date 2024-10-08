


**************************************************


Cで求める最小公倍数と最大公約数


**************************************************


C言語で書いたN個の数の最小公倍数と最大公約数を求めるプログラムです。Nは1024までにしてあります。

最小公倍数はlcm.cを、`cc lcm.c -o lcm`としてコンパイルし、最大公約数はgcd.cを`cc gcd.c -o gcd`としてコンパイルし、それぞれ、`./lcm a b c d ...`、`./gcd a b c d ...`として実行してください。

#コード：最大公約数

```gcd.c
#include  <stdio.h>
int gcd2(int x,int y) {
  int t;
  while(y!=0)
    t=x%y, x=y, y=t;
  return(x);
}

int main(int argc,char *argv[]) {
  int a[1024];          // 最大公約数を求める数の最大個数1024
  int i,d;
  for (i=1;i<argc;i++)
    sscanf(argv[i],"%d",&(a[i-1]));

  d=a[0];
  for (i=1;i<(argc-1)&&d!=1;i++)
    d=gcd2(a[i],d);
  printf("%d\n",d);
}
```

#コード：最小公倍数

```lcm.c
#include  <stdio.h>
int gcd2(int x,int y) {
  int t;
  while(y!=0)
    t=x%y, x=y, y=t;
  return(x);
}

int lcm2(int x,int y) {
    int xy=x*y;
    if (xy<0) xy=-xy;
    return(xy/gcd2(x,y));
}

int main(int argc,char *argv[]) {
  int a[1024];          // 最小公倍数を求める数の最大個数1024
  int i,d;
  for (i=1;i<argc;i++)
    sscanf(argv[i],"%d",&(a[i-1]));

  d=a[0];
  for (i=1;i<(argc-1)&&d!=1;i++)
    d=lcm2(d,a[i]);
  printf("%d\n",d);
}
```
