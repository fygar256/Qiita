


**************************************************


符号なし基数変換in C


**************************************************


#Cで、符号なしの基数変換（n進数）をします。

n進文字列から値へ、値からn進数文字列への変換を行う関数です。

ここでは、2~36進数まで扱えるようにしています。
nrdigitの値を変えると、扱える基数の範囲が変わります。

```nradix.c
//
//  符号なし基数変換
//
#include  <stdio.h>
#include  <stdlib.h>
#include  <string.h>
#include  <ctype.h>

char  nrdigit[]="0123456789abcdefghijklmnopqrstuvwxyz";

//
// 値から文字列へ
//
//    引数：val 値
//            r n進数のn
//          str 出力する文字バッファへのポインタ
//
//    返り値：strの長さ
//
int val2str(unsigned long val,int r,char *str) {
  int d,l,i;
  char  stack[1024];
  int   sp;

  if (val==0) {
    str[0]='0';
    str[1]='\0';
    return(1);
    }

  sp=0;
  l=0;
  while(val!=0) {
    d=val%r;
    val/=r;
    stack[sp++]=d;
    l++;
    }

  for(i=0;i!=l;i++)
    str[i]=nrdigit[stack[--sp]];

  str[i]='\0';
  return(strlen(str));
}

//
// 文字列から値へ
//
//    引数：inval n進数の数が入った文字列
//          radix n進数のn
//
//    返り値： unsigned long の値
//
//
unsigned long  str2val(char *inval,int radix) {
  int   s=0;
  char  *p;
  unsigned long  n=0;

  while(*inval!='\0') {
      p=index(nrdigit,tolower(*inval++));
      if (p==NULL)
        break;
      n=n*radix+(int)(p-nrdigit);
      }
  return(n);
}

int main(int argc,char *argv[]) {
    unsigned long  n;
    int   r;
    char  inval[1024];
    char  word[1024];

    if (argc!=3) {
      fprintf(stderr,"Usage %s <値> <基数>\n",argv[0]);
      exit(1);
      }

    sscanf(argv[1],"%s",inval);   //  get input value
    sscanf(argv[2],"%d",&r);      //  get radix

    if (r<2 || r>strlen(nrdigit)) {
        fprintf(stderr,"invalid radix.\n");
        exit(1);
        }
    n=str2val(inval,r);

    val2str(n,2,word); printf("%s binary\n",word);
    val2str(n,8,word); printf("%s octal \n",word);
    val2str(n,10,word); printf("%s decimal\n",word);
    val2str(n,16,word); printf("%s hexadecimal\n",word);
    val2str(n,24,word); printf("%s 24進数\n",word);
}

```
