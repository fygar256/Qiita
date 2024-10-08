


**************************************************


[joke] 簡単な暗号化プログラム。crypt.c decrypt.c by T.Maekawa and J.Hamada


**************************************************


簡単な暗号化プログラムです。

使い方：
```
cc crypt.c -o crypt
cc decrypt.c -o decrypt
```
としてコンパイルし、
```
crypt file.org file.out
```
として暗号化し、
```
decrypt file.out file.org
```
として復号化して下さい。
decryptを掛けると、cryptで暗号化したファイルは、全て同様に元に戻ります。復号キーはありません。

```crypt.c
#include    <stdio.h>
#include    <stdlib.h>
void main(int argc,char *argv[]) {
    if (argc!=3) {
        fprintf(stderr,"Usage: crypt file.in file.out\n");
        exit(-1);
    }
    FILE *ifp=fopen(argv[1],"rb");
    if (ifp==NULL) {
        fprintf(stderr,"File open error\n");
        exit(-1);
    }
    FILE *ofp=fopen(argv[2],"wb");
    if (ofp==NULL) {
        fprintf(stderr,"Output file open error\n");
        exit(-1);
    }
    int c;
    int bn=0x55;
    while(1) {
        c=fgetc(ifp);
        if (c==EOF)
            break;
        bn=bn^c;
        fputc(bn,ofp);
    }
}
// b1=a1^0x55 b2=a2^a1^0x55 b3=a3^a2^a1^0x55 ...
// c1=b1^0x55 c2=b1^b2 c3=b3^b2 ...
```

```decrypt.c
#include    <stdio.h>
#include    <stdlib.h>
void main(int argc,char *argv[]) {
    if (argc!=3) {
        fprintf(stderr,"Usage: crypt file\n");
        exit(-1);
    }
    FILE *ifp=fopen(argv[1],"rb");
    if (ifp==NULL) {
        fprintf(stderr,"File open error\n");
        exit(-1);
    }
    FILE *ofp=fopen(argv[2],"wb");
    if (ofp==NULL) {
        fprintf(stderr,"Output file open error\n");
        exit(-1);
    }
    int bn=0x55;
    int s;
    int cn;
    while(1) {
        s=bn;
        bn=fgetc(ifp);
        if (bn==EOF)
            break;
        cn=s^bn;
        fputc(cn,ofp);
    }
}
// b1=a1^0x55 b2=a2^a1^0x55 b3=a3^a2^a1^0x55 ...
// c1=b1^0x55 c2=b1^b2 c3=b3^b2 ...

```
実行例
```
gar@vaio:~/org/etc/crypt$ cat birth
hello world.
gar@vaio:~/org/etc/crypt$ crypt birth yet
gar@vaio:~/org/etc/crypt$ cat yet
=X4X7`}u[Qgar@vaio:~/org/etc/crypt$ decrypt yet birth2
gar@vaio:~/org/etc/crypt$ cat birth2
hello world.
gar@vaio:~/org/etc/crypt$ 
```
