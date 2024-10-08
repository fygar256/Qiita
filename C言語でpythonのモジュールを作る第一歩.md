


**************************************************


C言語でpythonのモジュールを作る第一歩


**************************************************


Cでpythonのモジュールを作る。第一歩。
frというテストモジュールを作ります。

###　環境

arch linux
python 3.11
gcc 13.2.1 20230801
GNU Make 4.4.1


### モジュール構成

```
fr.Open     Trueを返す。
fr.Close　　 Trueを返す
fr.fr       0.5を返す。
```

### モジュールを置く、ユーザーのlibraryディレクトリ

$HOME/lib/

ないなら、

```
mkdir -p $HOME/lib/
```

として、作っておきます。


### モジュール本体ソース

```fr.c
#include  <Python.h>
#include  <stdlib.h>
#include  <stdio.h>

//
// Open()
//
static PyObject* Open(PyObject *self,PyObject *args) {
    Py_RETURN_TRUE;
}

//
// Close()
//
static PyObject* Close(PyObject *self,PyObject *args) {
    Py_RETURN_TRUE;
}

//
// fr(): 値を返す
//

static PyObject* fr(PyObject *self,PyObject *args) {
    return Py_BuildValue("d", 0.5);
}

///*****************************************************************
///
/// モジュール定義
///
///*****************************************************************


//
// モジュール内のメソッドの定義する
//
// {
// "Open" メソッド名
//  Open メソッドの本体
//  METH_VARARGS は引数の定義, METH_NOARGS は引数なしのときの定義
//  "open random"         // メソッドの説明
// }

static PyMethodDef methods[] = {
    { 
     "Open",                // メソッド名
      Open,                 // メソッド本体
      METH_NOARGS,          // 引数なしのときの定義
      "open"         // メソッドの説明
    },
    {"Close", Close, METH_NOARGS, "close"},
    {"fr", fr, METH_NOARGS, "return a number"},
    //{"fr", fr, METH_VARARGS, ""}, 引数を取る場合
    {NULL,NULL,0,NULL},  // 番兵
};

// モジュールの定義
static struct PyModuleDef module =
{
    PyModuleDef_HEAD_INIT,
    "fr",  // モジュール名。
    "test",  // モジュールの説明
    -1,  // モジュールのインタプリタのステートのサイズ
    methods  // メソッド群定義
};

// モジュールの初期化
PyMODINIT_FUNC PyInit_fr(void)
{
    return PyModule_Create(&module);
}

```

### makefile

```
# Makefileのコマンド行の先頭キャラクタをスペースに
.RECIPEPREFIX :=$(RECIPEPREFIX) <space>

CC          = gcc
RM          = rm
CP          = cp
CSUFFIX     = .c
LSUFFIX     = .so

# 動的ライブラリの名前
TARGET      = fr$(LSUFFIX)

SRCS        = fr.c          # ソースファイルの列挙
# ライブラリディレクトリ
LIBDIRNAME  = $${HOME}/lib/
# コンパイラに渡すオプション
CFLAGS      = -Wall -O2

# makeとすると動的ライブラリを作成する
$(TARGET): $(SRCS)
    gcc -I/usr/include/python3.11 -fPIC -Wall -shared -o $@ $^

# make installとすると、ライブラリを当該ディレクトリにインストール
install: $(TARGET)
    $(CP) $(TARGET) $(LIBDIRNAME)

# make clean とすると .o ファイル削除
clean:
    $(RM) -rf $(TARGET)

# make all とすると最初からやる
all: clean install

```

### テストプログラム

実行する前に、モジュールをオプショナルな場所からインポートできるように、

```
export PYTHONPATH="$HOME/lib:$PYTHONPATH"
```

として、パスを通しておいてください。


```frtest.py
#!/usr/bin/python3
import fr
print(fr.Open())
print(fr.fr())
print(fr.Close())
```

### 実行結果

```
$ ./frtest.py
True
0.5
True
```

