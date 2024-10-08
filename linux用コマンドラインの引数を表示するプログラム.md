


**************************************************


linux用コマンドラインの引数を表示するプログラム


**************************************************



Linuxで、GUIのメニューやアプリケーション・ランチャーからアプリケーションを起動しようとしても起動しなかったりしたら、どこがいけないのか調べたいですよね。このプログラムは、アプリケーション・ランチャーからコマンドラインの引数がコマンドにどのように渡されるかを調べるプログラムです。

# 使い方

・アプリケーション・ランチャーのコマンドライン行のコマンドの頭にparamを付けて下さい。
`param [-f=file] command arg1 arg1 ...`

・端末に出力したければ、
`param -f=/dev/pts/0 command arg1 arg2 ...`
として下さい。
どのターミナルエミュレータが、どのデバイスファイル（/dev/pts/n)に割り当てられているかは、ターミナルエミュレータで、
`tty`
とすると表示されます。

・ファイルに出力したければ、
`param -f=file command arg1 arg2 ...`
として下さい。
ファイルが作れなければエラーを出力せず、そのままコマンドを実行します。

・コマンドに--nopを指定すると、引数の表示だけして何にもしません。

`param -f=/dev/pts/0 --nop arg1 arg2`

# 使用例

・引数を/dev/pts/0に表示してwineでKindle.exeを呼び出す。

```
env WINEPREFIX=.wine WINEARCH=win32 param -f=/dev/pts/0 wine "/home/$USER/.wine/drive_c/Program Files/Amazon/Kindle/Kindle.exe"
```


# コンパイル & インストール

`cc param.c -o param`
としてコンパイルし、適当なパスの通ったディレクトリにparamをコピーして下さい。


# ソースコード

```param.c
#include  <stdio.h>
#include  <stdlib.h>
#include  <string.h>
//
//
// 引数のリストを出力してcommand に引数を渡して呼び出す
// --nop が指定されたら、コマンドを呼び出さないで引数のリストを出力する。
//
// -f=file が指定されたら、file に引数を出力する。
// -f=file が指定されなかったらstdoutに出力する。
//
// 特定の端末エミュレータに出力するには-f=/dev/pts/<n>とする。
// ある端末にどのデバイスファイルが割り当てられているかは、
// ttyコマンドで調べる。
// ttyコマンドは、
// $ tty
// /dev/pts/0
// という出力がされる。
//
//
int main(int argc,char *argv[]) {
  int idx;
  FILE *fp;
  char line[1024]="";
  if (argc==1 || (argc==2 && strcmp(argv[1],"--help")==0)) {
    fprintf((FILE *)stderr,"usage: param [-v=file] <command|--nop> [引数1] [引数2] [引数3] ...\n");
    exit(1);
    }

  idx=1;
  if (strncmp(argv[1],"-f=",3)==0) {
      idx=2;
      fp=fopen(&(argv[1][3]),"w");
      if (fp!=NULL) {
          if (argc>=2) {
              for (int i=idx;i<argc;i++)
                  fprintf(fp,"%s\n",argv[i]);
              }
          fclose(fp);
          }
       }

  for (int i=idx;i<argc;i++) {
    strcat(line,argv[i]);
    if (i!=argc-1)
       strcat(line," ");
    }
  printf("%s\n",line);
  if (strncmp(argv[idx],"--nop",5)==0)
    return(0);
  return(system(line));
}

```
