


**************************************************


linux C言語：ユーザーのライブラリを作る。makefile のテンプレート、getch(),kbhit()を例にとって。


**************************************************



LinuxのCプログラミングにちょっと慣れてきたら、自分のライブラリが欲しくなります。

静的ライブラリを活用することで、複数の実行ファイルに共通する関数の管理がやりやすくなります。

makeを使って、ターミナルのCUIでやります。
ここでは、getch()を例に取り、getch()と、kbhit()で構成されるlibgetch.aという
静的ライブラリを開発することを目的とします。
kbhit()はキーボードが押されたかどうかを判定する関数で、getch()は、コンソールからのエコーバックなしの1文字入力です。


# 環境

Ubuntu22.04LTS
GNU Make 4.3
gcc (Ubuntu 11.3.0-1ubuntu1~22.04) 11.3.0

LinuxのCの開発環境がインストールされているものとしておいて下さい。

# ディレクトリ構成

以下のディレクトリ構成の開発環境だとします。


```
/home/user/
├── getch　　　　　　# カレントディレクトリ
│   ├── getch.c
│   ├── getch.h
│   ├── kbhit.c
│   ├── main.c
│   └── makefile   # libgetch.aと実行ファイルを作るためのmakefile
├── include        # ユーザー用インクルードディレクトリ
├── lib            # ユーザー用ライブラリディレクトリ
：
：
```

# 必要ファイル

#### getch.h

```getch.h
extern int getch();
extern int kbhit();
```

#### getch.c

```getch.c
#include <termios.h>
#include <unistd.h>
#include <stdio.h>
#include <fcntl.h>
#include <getch.h>

int getch(){
      struct termios oldt, newt;
      int c;
      tcgetattr( STDIN_FILENO, &oldt );
      newt = oldt;
      newt.c_lflag &= ~ICANON;
      newt.c_lflag &= ~ECHO;
      tcsetattr( STDIN_FILENO, TCSANOW, &newt );
      c = getchar();
      tcsetattr( STDIN_FILENO, TCSANOW, &oldt );
      return c;
}
```

#### kbhit.c


```kbhit.c
#include <termios.h>
#include <unistd.h>
#include <stdio.h>
#include <fcntl.h>
#include <getch.h>

int kbhit(){
      struct termios oldt, newt;
      int c;
      int oldf;    
      tcgetattr(STDIN_FILENO, &oldt);
      newt = oldt;
      newt.c_lflag &= ~(ICANON | ECHO);
      tcsetattr(STDIN_FILENO, TCSANOW, &newt);
      oldf = fcntl(STDIN_FILENO, F_GETFL, 0);
      fcntl(STDIN_FILENO, F_SETFL, oldf | O_NONBLOCK);    
      c = getchar();    
      tcsetattr(STDIN_FILENO, TCSANOW, &oldt);
      fcntl(STDIN_FILENO, F_SETFL, oldf);    
      if(c != EOF) { ungetc(c, stdin); return 1; }    
      return 0;
}
```
#### main.c


```main.c
#include <stdio.h>
#include <getch.h>

int main() {
  while(1) {
    if (kbhit()) {
        int key=getch();
        printf("%c",key);
        }
    }
}
```

#### makefile

makefile のテンプレートとしても使えます

```makefile
# makefileのレシピ行の最初のキャラクタをスペースにします
.RECIPEPREFIX :=$(RECIPEPREFIX) <space>

# ユーザーネーム
USER        = user

CC          = gcc                      # コンパイラはgccを使うものとします
RM          = rm
CP          = cp
CSUFFIX     = .c
OSUFFIX     = .o
LSUFFIX     = .a
HSUFFIX     = .h

# 静的ライブラリの名前
TARGET      = getch

# 実行ファイル名
MAIN        = main

SRCS        = getch.c kbhit.c main.c   # ソースファイルの列挙
OBJS        = $(SRCS:$(CSUFFIX)=.o)    # オブジェクトファイルの名前定義 

LIBNAME     = lib$(TARGET)$(LSUFFIX)   # 静的ライブラリのファイルネーム
HEADER      = $(TARGET)$(HSUFFIX)      # ヘッダファイルのファイルネーム

# ライブラリディレクトリ
LIBDIRNAME  = /home/$(USER)/lib/

# インクルードディレクトリ）
INCDIR      = /home/$(USER)/include/

# コンパイラに渡すオプション（インクルードディレクトリ）
INCLUDE     = -I$(INCDIR)

# コンパイラに渡すオプション（ライブラリディレクトリ）
LDFLAGS     = -L$(LIBDIRNAME)

# コンパイラ渡すオプション（静的ライブラリのファイル指定)
LIBS        = -l$(TARGET)

# コンパイラに渡すオプション
CFLAGS      = -Wall -O2 $(INCLUDE)

# 一つづつオブジェクトファイルを生成
$(CSUFFIX).$(OSUFFIX):
    $(CC) -c $<

# make とすると静的ライブラリを作成する
$(LIBNAME): inc $(filter-out $(MAIN)$(OSUFFIX),$(OBJS))
    ar rcs $(LIBNAME) $(filter-out inc,$^)

# make incとすると、ヘッダフィルを当該ディレクトリにインストール
#                         ディレクトリのアクセス権をセット
inc: $(HEADER)
    $(CP) $(HEADER) $(INCDIR)

# make main とすると実行ファイルを作成
$(MAIN): inc $(MAIN)$(OSUFFIX) $(LIBNAME) install
    $(CC) -o $(MAIN) $(MAIN)$(OSUFFIX) $(CFLAGS) $(LIBS) $(LDFLAGS)

# make setとすると、静的ライブラリとヘッダファイルを当該ディレクトリにインストール
install: $(LIBNAME) inc
    $(CP) $(LIBNAME) $(LIBDIRNAME)

# make clean とすると .o ファイル削除
clean:
    $(RM) -rf $(OBJS) $(LIBNAME)

# make all とすると最初からやる
all: clean inc $(MAIN)
```
# ライブラリ作成


これで、makeとすると、ヘッダファイルgetch.hが、/home/user/includeにインストールされ、静的ライブラリlibgetch.aが、/home/user/libにインストールされます。
make main とするとテストプログラムのmainという実行ファイルが作成されます。


# makefileのmake のコマンド

```
$ make             # 静的ライブラリlibgetch.aを作成する
$ make inc         # ヘッダファイルをインクルードディレクトリにインストール
$ make main        # 実行ファイルを作成
$ make install     # 静的ライブラリとヘッダファイルを当該ディレクトリにインストール
$ make clean       # オブジェクトファイル削除、静的ライブラリlibgetch.aを削除
$ make all         # オブジェクをファイルを削除、静的ライブラリ削除、
                   # また作成、実行ファイル作成、静的ライブラリとヘッダファイルを                  
                   # 当該ディレクトリにインストール
```


# 注意点

・コンパイラが静的ライブラリ(.a)をリンクする場合は、例えばA.oがB.aに依存しているなら、A.oをB.aの前に引数で与えなくてはなりません。
・静的ライブラリ(.a)は、オブジェクトファイル(.o)をarで束ねただけのものです。
・-lで与える名前は、libgetch.aでもいいですが、省略してgetchとします。



# make allの実行をした結果のディレクトリ構造

```
/home/user/
├── getch　　　　　　# カレントディレクトリ
│   ├── getch.c
│   ├── getch.h
│   ├── getch.o
│   ├── kbhit.c
│   ├── kbhit.o
│   ├── libgetch.a
│   ├── main
│   ├── main.c
│   ├── main.o
│   └── makefile
├── include
│   └── getch.h
├── lib
:   └── libgetch.a
:
```

# 環境変数

最後に、環境変数にPATHをセットして終わりです。

```
export C_INCLUDE_PATH="/home/user/include:$C_INCLUDE_PATH"
export LIBRARY_PATH="$LIBRARY_PATH:/home/user/lib"
```

これで、`gcc main.c -lgetch`でコンパイルできるようになります。








