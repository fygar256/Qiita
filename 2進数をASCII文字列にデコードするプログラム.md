


**************************************************


2進数をASCII文字列にデコードするプログラム


**************************************************


2進数コードをASCIIコードにデコードするCプログラムです。
Linuxのターミナルで動きます。
FacebookのLinux Fan Groupにたまに2進数の暗号が載るので、読むために作りました。
`cc bindecode.c -o bindecode`としてコンパイルしてください。
2進数のダンプなら、`echo "string" | xxd　-b`でできます。

```bindecode.c
#include    <stdio.h>
#include    <stdlib.h>
int main(int argc,char *argv[]) {
    FILE *fp;
    int c;
    if (argc!=2) { fprintf(stderr,"Usage: bindecode <file>\n"); exit(1); }
    fp=fopen(argv[1],"r");
    if (fp==NULL) { fprintf(stderr,"File open error\n"); exit(1); }
    for(int b=0;(c=fgetc(fp))!=EOF;) {
        switch(c) {
            case ' ':
            case '\t':
            case '\n':
                printf("%c",b);
                b=0;
                break;
            case '1':
                b=(b<<1)|1;
                break;
            case '0':
                b<<=1;
                break;
            default:
            }
        }
    fclose(fp);
    exit(0);
}


```
