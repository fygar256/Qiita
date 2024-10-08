


**************************************************


Brainfuck compiler in C


**************************************************


Cで書いたbrainfuck compilerです。
30分で書けました。

ファイルからbrainfuckのソースを読み込んで、C言語に落としたものを、標準出力に出力します。リダイレクトして使用します。

出力したC言語のコードは、完全に機械独立で、一般のCコンパイラに通ると思います。

compile:
cc bfc.c -o bfc

使い方：
./bfc <file>
for ex.
$ ./bfc sample.bf >sample.c

```bfc.c
#include    <stdio.h>
#include    <stdlib.h>
void main(int argc,char *argv[]) {
    FILE *fp;
    int  c;
    if (argc!=2) {
        fprintf(stderr,"Usage: bfc <file>\n");
        exit(1);
    }
    fp=fopen(argv[1],"rt");
    printf("#include    <stdio.h>\n");
    printf("void main() {\n");
    printf("char array[30000]={0};\n");
    printf("char *ptr=array;\n");
    while((c=fgetc(fp))!=EOF) {
        switch (c) {
            case '>':
                printf("ptr++; ");
                break;
            case '<':
                printf("ptr--; ");
                break;
            case '+':
                printf("(*ptr)++; ");
                break;
            case '-':
                printf("(*ptr)--; ");
                break;
            case '.':
                printf("putchar(*ptr); ");
                break;
            case ',':
                printf("*ptr=getchar(); ");
                break;
            case '[':
                printf("while(*ptr){ ");
                break;
            case ']':
                printf("} ");
                break;
            default:
            }
    }
    fclose(fp);
    printf("}\n");
    exit(0);
}

```
