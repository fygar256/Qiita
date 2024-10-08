


**************************************************


for peace 簡単で小さい文字列暗号化と復号。


**************************************************



英字小文字で入力された文字列を、doubleの範囲で数値暗号化し、復号をします。

このプログラムは、数値暗号化です。`helloencode string`として、stringにはスペースや句読点を含まない英字小文字の文字列を指定して下さい。
コンパイルは、`cc helloencode.c -o helloencode`として下さい。

```helloencode.c
#include    <stdio.h>
#include    <string.h>
#include    <ctype.h>
#include    <stdlib.h>
void main(int argc,char *argv[]) {
    if (argc!=2) {
        fprintf(stderr,"Usage: helloencode string\n");
        exit(-1);
    }
    double  d=0;
    int c;
    for(int i=strlen(argv[1]);i!=-1;i--) {
        c=argv[1][i];
        if (c<'a'||c>'z')
            continue;
        d=d*27+c-'a'+1;
    }
    printf("%lf\n",d);
}




```

このプログラムはforpeaceという文字列を出力します。
コンパイルは、`cc forpeace.c -o forpeace -lm`として下さい。

```forpeace.c
#include  <stdio.h>
#include  <math.h>
int main() {
  for (double m=53481362055;(int)m;m/=27)
   putchar(fmod(m,27)+'a'-1);
  putchar('\n');
}

```
このコードは、helloworldという文字列を出力します。
コンパイルは、`cc hello.c -o hello -lm`として下さい。
```hello.c
#include  <stdio.h>
#include  <math.h>
int main() {
  for (double m=34085980284272;(int)m;m/=27)
   putchar(fmod(m,27)+'a'-1);
  putchar('\n');
}

```
このコードは、数値から文字列に復号します。
`cc hellodecode.c -o hellodecode -lm`としてコンパイルして下さい。

```hellodecode.c
#include  <stdio.h>
#include  <math.h>
#include  <stdlib.h>
int main(int argc,char *argv[]) {
  double t;
  if (argc!=2) {
	  fprintf(stderr,"%lf");
  }
  sscanf(argv[1],"%lf",&t);

  for (double m=t;(int)m;m/=27)
   putchar(m%27+'a'-1);
  putchar('\n');
}
```
27で回しているのは、余りが1つ必要だからです。

おまけ：answer for spinning wheel
```answer.py
#!/usr/bin/python3
def answer(n):
    return "The answer is 1."

if __name__=="__main__":
    print(answer(3))
    exit(0)
```
