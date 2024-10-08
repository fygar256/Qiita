


**************************************************


2進数を利用した組み合わせ(Combination)生成による与えられた文字列中の全ての文字列を返すコード


**************************************************


2進数を利用した、Combination生成による、与えられた文字列に含まれる全ての文字列を返すコード。

Combinationを求めるアルゴリズムは、技術評論社刊、「C言語による最新アルゴリズム辞典」（奥村晴彦　著）を参考にしました。（2進数を用いた非常に巧妙な方法です）

順序は入れ替わりません。

for terminal.

```alice.c
#include	<stdio.h>
#include	<stdlib.h>
#include	<string.h>
int	N; 
int	K;
int	t;
typedef unsigned long set;
char	word[1024];
#define	first(n)	((set) ((1U << (n)) -1U))

set nextset(set x)
{
		set smallest,ripple,new_smallest,ones;

	smallest = x & -x;
	ripple = x +smallest;
	new_smallest = ripple & -ripple;
	ones =((new_smallest /smallest) >>1)-1;
	return ripple| ones;
}
void printset(set s)
{
	int	i;

	for(i=1;i<=N;i++) {
	if (s & 1) printf("%c",word[i-1]);
	s>>=1;
	}
	printf("\n");
}

void conv()
{
	int i;
	set x;

	x=first(K);
	while(! (x&~first(N))) {
		printf("%5d :",t); printset(x);
		x=nextset(x); t++;
	}
}
int main(int argc,char *argv[]) {

	if (argc!=2) {
		fprintf(stderr,"Usage alice word\n");
		exit(0);
		}

	sscanf(*(++argv),"%s",word);
	N=strlen(word);
	t=1;
	for(K=1;K<=N;K++) {
		conv();
		}
}
```

特に環境依存的なところはないので、Cの開発環境があれば、簡単にコンパイルができます。

To compile: `cc alice.c -o alice`
Usage: `alice word`
実行結果：

```
$ alice alice
    1 :a
    2 :l
    3 :i
    4 :c
    5 :e
    6 :al
    7 :ai
    8 :li
    9 :ac
   10 :lc
   11 :ic
   12 :ae
   13 :le
   14 :ie
   15 :ce
   16 :ali
   17 :alc
   18 :aic
   19 :lic
   20 :ale
   21 :aie
   22 :lie
   23 :ace
   24 :lce
   25 :ice
   26 :alic
   27 :alie
   28 :alce
   29 :aice
   30 :lice
   31 :alice
$
```
