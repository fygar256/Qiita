


**************************************************


[Joke] 最大の圧縮率のファイル圧縮プログラムtmcomp


**************************************************


tmcomp.c
tmcompはどんなに大きいファイルでも最小のファイルサイズに圧縮する圧縮プログラムです。非可逆圧縮で、一旦圧縮したファイルは再現できません。
cc tmcomp.c -o tmcompとしてコンパイルしてください。
実行は、./tmcomp fileとします。
実行するとファイルを出力し、メッセージを出して終了します。元のファイルは削除しません。
複数のファイルを圧縮する場合にはarを使って一旦アーカイブしたものを圧縮します、

ar q file.a file1 [file2 ...]
./tmcomp file.a

圧縮が終わった出力ファイルは0バイトの長さを持ち.tmcの拡張子を持ちます。
圧縮率はファイルサイズが0の場合は1、その他の場合、無限大です。
どんなに大きいファイルでも0バイトに圧縮できる世界最小圧縮サイズのソフトです。

```tmcomp.c
#include    <stdio.h>
#include    <stdlib.h>
#include    <string.h>
void main(int argc,char *argv[]) {
    if (argc!=2) {
        fprintf(stderr,"Usage tmcomp file\n");
        exit(1);
    }
    char s[256];
    strcpy(s,argv[1]);
    FILE *ifp=fopen(s,"rb");
    if (ifp==NULL) {
        fprintf(stderr,"Source file open error.\n");
        exit(1);
    }
    strcat(s,".tmc");
    FILE *ofp=fopen(s,"wb");
    while(!(fgetc(ifp)==EOF));
    fclose(ofp);
    fclose(ifp);

    fprintf(stderr,"Compression successfully completed.\n");
    fprintf(stderr,"output file written.\n");
}
```
