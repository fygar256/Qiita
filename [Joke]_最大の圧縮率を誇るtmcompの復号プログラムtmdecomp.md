


**************************************************


[Joke] 最大の圧縮率を誇るtmcompの復号プログラムtmdecomp


**************************************************


最大の圧縮率を誇るtmcomp用復号プログラムtmdecompです。
非可逆圧縮なので、元のファイルは戻りませんが、同じ名前のファイルを出力します。
ファイル内容はどんなファイルを復号しても0x0aです。
あれだけ非可逆圧縮と断っていたので、元のファイルが戻らなくても、怒らないで下さい。
`cc tmdecomp.c -o tmdecomp`でコンパイルし、
`./tmdecomp file.tmc`で実行して下さい。

```tmdecomp.c
#include    <stdio.h>
#include    <stdlib.h>
#include    <string.h>
void main(int argc,char *argv[]) {
    if (argc!=2) {
        fprintf((stderr),"Error invalid argument.");
        exit(1);
        }
    char *s=argv[1];
    if (strcmp(&(s[strlen(s)-4]),".tmc")!=0) {
        fprintf((stderr),"It is not .tmc file.\n");
        exit(1);
        }
    char spi[strlen(s)-4];
    strncpy(spi,s,strlen(s)-4);
    FILE *efp=fopen(s,"r");
    if (efp==NULL) {
        fprintf(stderr,"File does not exsist.\n");
        exit(1);
        }
    FILE *ofp=fopen(spi,"wb");
    fputc((int)0xa,ofp);
    fclose(efp);
    fclose(ofp);
}
```
