


**************************************************


GAME言語とC言語で、エラトステネスの篩により素数を求める


**************************************************



エラトステネスのふるいで素数を求めるCのプログラムとGAME言語スクリプトです。
アルゴリズムは、「［改訂新版］C言語による標準アルゴリズム事典」奥村晴彦著によります。

# GAME言語

```sieve.gm
#!/usr/bin/gamelinux
1*** sieve.gm エラトステネスの篩により素数を求めるプログラム
2*** アルゴリズムは、「C言語による標準アルゴリズム事典」奥村晴彦著によります。
6*** 
10  "Primes up to " M=? N=(M-3)/2
20  F=0
100  C=1
110  "2 "
120  J=0,N F:J)=1 @=J+1
150  J=0,N
160     ;=#F:J) #=200
170         P=J*2+3
175         ?=P " "
180         ;=J+P<=N K=J+P,N F:K)=0 @=K+P
190         C=C+1
200  @=J+1
210 / ?=C " PRIMES"/
220 #=-1

```

# C言語バージョン

```sieve.c
#include  <stdio.h>
#include  <stdlib.h>

int primes(int n) {
    char f[n];
    int c=1;
    printf("2 ");
    for (int j=0;j<=n;j++) f[j]=1;
    for (int j=0;j<=n;j++)
       if (f[j]) {
           int p=j*2+3;
           printf("%d ",p);
           for (int k=j+p;k<=n;k+=p) f[k]=0;
           c++;
           }
    return(c);
}

int main(int argc,char *argv[]) {
    int m=atoi(argv[1]),c;
    c = primes((m-3)/2);
    printf("\n%d Primes\n",c);
}
```

