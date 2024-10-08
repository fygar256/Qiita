


**************************************************


超簡単なアルゴリズムによる素因数分解　in bash,zsh,sh,C++,C,Python,GAME言語,Perl


**************************************************


超簡単なアルゴリズムによる素因数分解です。
与えられた数を2から一つずつインクリメントする変数で片っ端から割っていき、余りが出なかったらその数を因数として登録するという、最も原始的なアルゴリズムです。多分、一番簡単なのではないでしょうか。
nには自然数を与えて下さい。
shとC言語とpythonとGAME言語とC++とperlで書かれています。
GAME言語のバージョンはおまけです。いらないでしょうけど、古のGAME言語を懐かしむ人がいたら見て下さい。

# sh

```factor.sh
#!/bin/bash
function PrimeFactorization() {
  i=2
  j=0
  num=$1
  arr=()
  if test $num -eq 0; then
     return
  fi
  if test $num -eq 1; then
     return
  fi
  while test $num -ne 1
  do
     if test $((num % i)) -eq 0; then
         arr+="$i "
         num=$((num/i))
         j=$((j+1))
     else
         i=$((i+1))
     fi
  done
  return
}

PrimeFactorization $1
echo "$1 : ${arr[@]}"
```

chmod +x factor.bash
./factor.bash <n>
として動かして下さい。

# C

```factor.c
#include  <stdio.h>
#include  <stdlib.h>
int PrimeFactorization(uint num,uint *arr)
{
  int i=2,j=0;
  if (num==0)
    return(0);
  else if (num==1) {
    return(0);
    }
  while(num!=1) {
    if (num%i==0) {
      arr[j]=i;
      num/=i;
      j++;
      }
    else i++;
    }
  return(j);
}

int main(int argc,char *argv[]) {
    uint arr[10000],n=atoi(argv[1]),nop;
    nop=PrimeFactorization(n,arr);
    printf("%d : ",n);
    for(int i=0;i<nop;i++)
      printf("%d ",arr[i]);
    printf("\n");
    return(0);
}
```

cc factor.c -o factor としてコンパイルして下さい。
その後、
factor \<n\>
として動かして下さい。


# python

```factor.py
#!/usr/bin/python3
import sys
def PrimeFactorization(n):
  i=2
  j=0
  f=[]
  if n==0:
    return []
  elif n==1:
    return []
  while(n!=1):
    if n%i==0:
      f+=[i]
      n=n/i
      j=j+1
    else:
      i=i+1
  return f

n=int(sys.argv[1])
f=PrimeFactorization(n)
print(n,":",f)
```

chmod +x factor.py
./factor.py \<n\>
として動かして下さい

# GAME言語

```factor.gm
#!/usr/bin/gamelinux
1000 "Input natural number : " N=?
1010 Z=$0000 !=1100
1020 ?=N ":"
1030 ;=J I=0,J-1 ?=Z(I) " " @=I+1
1040 /
1050 #=-1
1100#
1101# Sub routine of PrimeFactorization
1103#
1105 I=2 J=0 M=N
1110 ;=M=0 J=0 ]
1130 ;=M=1 ]
1135   H=M/I
1136   ;=%1=0 Z(J)=I M=H J=J+1 #=1130
1140     I=I+1 #=1130
```

gamelinux factor.gm
として動かして下さい。

# C++


```factor.cpp
#include  <iostream>
using namespace std;
int PrimeFactorization(int num,int arr[])
{
  int i=2,j=0;
  if (num==0)
    return(0);
  else if (num==1) {
    return(0);
    }
  while(num!=1) {
    if (num%i==0) {
      arr[j]=i;
      num/=i;
      j++;
      }
    else i++;
    }
  return(j);
}

int main(int argc,char *argv[]) {
    int arr[10000],n=atoi(argv[1]),nop;
    nop=PrimeFactorization(n,arr);
    cout << n << " : ";
    for(int i=0;i<nop;i++)
      cout << arr[i] << " ";
    cout << endl;
    return(0);
}
```

g++ factor.cpp -o factorcpp
としてコンパイルし、
factorcpp \<n\>
として動かして下さい。

# perl

```factor.pl
#!/usr/bin/perl
sub primefactorization {
  local($n,$i,$j,@f);
  $n=$_[0];
  $i=2;
  $j=0;
  @f=();
  if ($n==0) {
    return @f;
    }
  while($n!=1) {
    if ($n%$i==0) {
      push(@f,$i);
      $n=$n/$i;
      $j=$j+1;
      }
    else {
      $i=$i+1;
      }
    }
  return @f;
}

$n=$ARGV[0];
@l=&primefactorization($n);
$,=',';
print "$n:@l\n";
```
`./factor.pl <n>`として動かしてください。
