


**************************************************


非正則恒等置換阿弥陀籤（Ubuntu Terminal）


**************************************************


辿っていったら、必ず同じラインに辿り着くという、役に立たない阿弥陀籤を生成するプログラムです。Ubuntu18.04で動作確認しています。

ASCII文字を使ったのとUTF-8記号文字を使ったのと２つ載せておきます。


Cで書かれています。
コンパイルは、
`$ cc amida.c -o amida`でして下さい。
実行は、
`$ ./amida 12 15`とします。最初の12が、縦のライン数、次の15が阿弥陀籤の長さです。
パラメータは、省略することも出来ます。
For ex.
①`$ ./amida 12` 縦のライン数12で阿弥陀籤を作る。
②`$ ./amida`　デフォルトのサイズの阿弥陀籤を作る。

アルゴリズムは、ざっと言うと、【阿弥陀籤を鏡に映すと、反対に辿り、同じところに着く】、という原理に基づいています。

# utf-8記号文字を使ったソース

```amida.c
#include  <stdio.h>
#include  <stdlib.h>
#include  <time.h>
#define SP "  "
#define VL "┃"
#define HL "━━"
#define LB "┫"
#define RB "┣"
#define VLSP (VL SP)
#define LBSP (LB SP)
#define RBHL (RB HL)
#define rnd(x) (rand()%x)
#define XM  300
#define YM  300
static  int bar[XM][YM];

void make_amida(int xmx,int ymx) {
  int i,x,y;

  for(y=0;y<YM;y++)
    for(x=0;x<XM;x++)
      bar[x][y]=0;

  for(i=0;i<xmx*ymx/4;i++) {
    x=rnd(xmx),y=rnd(ymx/2+1);
    if ( (x==0||(x>0&&bar[x-1][y]==0)) &&
       (x==xmx||(x<xmx&&bar[x+1][y]==0)))
      bar[x][y]=bar[x][ymx-y]=1;
  }
}
      
void extra_line(int xmx,int n) {
  int i,x;
  for(i=0;i<n;i++) {
    for(x=0;x<=xmx;x++)
      printf("%s",VLSP);
    printf("\n");
    }
}

void disp_amida(int xmx,int ymx) {
  int f,x,y;

  extra_line(xmx,3);
  for(y=0;y<ymx;y++) {
    f=0;
    for(x=0;x<xmx+1;x++)
      if (bar[x][y]) printf("%s",RBHL),f=1;
      else printf("%s",f?LBSP:VLSP),f=0;
    printf("\n");
    }
  extra_line(xmx,3);
}

void main(int argc,char **argv) {
  int xmx,ymx;

  srand((unsigned)time(NULL));
  xmx=20,ymx=20;
  switch(argc) {
    case 1:
      break;
    case 2:
      sscanf(argv[1],"%d",&xmx);
      break;
    case 3:
      sscanf(argv[1],"%d",&xmx);
      sscanf(argv[2],"%d",&ymx);
      break;
    }
  if (ymx%2==0) ymx--;
  if (ymx<=14) ymx=15;
  if (xmx<3) xmx=3;
  xmx--;
  make_amida(xmx,ymx);
  disp_amida(xmx,ymx);
  exit(0);
}
```

#ASCII文字（UTF-8曖昧幅文字を使わない）ソース

```amida.c
#include  <stdio.h>
#include  <stdlib.h>
#include  <time.h>
#define SP "  "
#define VL "|"
#define HL "--"
#define LB "+"
#define RB "+"
#define VLSP (VL SP)
#define LBSP (LB SP)
#define RBHL (RB HL)
#define rnd(x) (rand()%x)
#define XM  300
#define YM  300
static  int bar[XM][YM];

void make_amida(int xmx,int ymx) {
  int i,x,y;

  for(y=0;y<YM;y++)
    for(x=0;x<XM;x++)
      bar[x][y]=0;

  for(i=0;i<xmx*ymx/4;i++) {
    x=rnd(xmx),y=rnd(ymx/2+1);
    if ( (x==0||(x>0&&bar[x-1][y]==0)) &&
       (x==xmx||(x<xmx&&bar[x+1][y]==0)))
      bar[x][y]=bar[x][ymx-y]=1;
  }
}
      
void extra_line(int xmx,int n) {
  int i,x;
  for(i=0;i<n;i++) {
    for(x=0;x<=xmx;x++)
      printf("%s",VLSP);
    printf("\n");
    }
}

void disp_amida(int xmx,int ymx) {
  int f,x,y;

  extra_line(xmx,3);
  for(y=0;y<ymx;y++) {
    f=0;
    for(x=0;x<xmx+1;x++)
      if (bar[x][y]) printf("%s",RBHL),f=1;
      else printf("%s",f?LBSP:VLSP),f=0;
    printf("\n");
    }
  extra_line(xmx,3);
}

void main(int argc,char **argv) {
  int xmx,ymx;

  srand((unsigned)time(NULL));
  xmx=20,ymx=20;
  switch(argc) {
    case 1:
      break;
    case 2:
      sscanf(argv[1],"%d",&xmx);
      break;
    case 3:
      sscanf(argv[1],"%d",&xmx);
      sscanf(argv[2],"%d",&ymx);
      break;
    }
  if (ymx%2==0) ymx--;
  if (ymx<=14) ymx=15;
  if (xmx<3) xmx=3;
  xmx--;
  make_amida(xmx,ymx);
  disp_amida(xmx,ymx);
  exit(0);
}
``` 


# この二つのソースの違い

違いは下記のとおりです。（diffの出力です）

曖昧幅文字を使っているのでは、ソースがちょっと、UTF-8記号文字の定義が違います。
他のプラットホームの人は、適宜、`#define`の所の記号文字を変える必要があるかも知れません。

```
5,8c5,8
< #define VL "┃"
< #define HL "━━"
< #define LB "┫"
< #define RB "┣"
---
> #define VL "|"
> #define HL "--"
> #define LB "+"
> #define RB "+"
31c31
< 
---
>       

```
