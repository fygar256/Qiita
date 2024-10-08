


**************************************************


Python+Cursesで、大昔のキャラクタゲームを再現してみよう。その２


**************************************************



昔懐かし、Robotsゲームを、python3とcursesで再現してみました。
私の投稿のCから、pythonへの移植版です。
元の記事は、これです→<a href="https://qiita.com/fygar256/items/441a3a1e8cee3d67d3e4">【昔懐かし、CBM-3032のようなキャラクタゲームをncurseswで再現しやう。】</a>
まだ、pythonには慣れてないので、ちょっと手古摺りましたが、何かのご参考になれば幸いです。

このゲームは、途中で、Ctrl-Cで止めると、ターミナルの設定がおかしくなってしまうので、
ゲーム更新時の、"Try Again? [y/n]" で、'n'を押して止めて下さい。

Robotsゲームですが、名前を変えて、「maneaters」にしてあります。
拙いプログラムですが、よろしくお願いします。


```maneaters.py
#!/usr/bin/python3

import curses
import random
import locale

xsize = 40  # ゲーム画面サイズx
ysize = 23  # ゲーム画面サイズy
gamestat = 0  # ゲームの状態　０：プレイ中　１：負け　３：勝ち
stdscr = curses.initscr()
CRASH = "Ｘ"
SPACE = "　"


def putchara(x, y, str):
    #
    # 画面に文字を表示する関数。
    # 全角単位で扱っているので、
    # x座標を２倍しています。
    #
    stdscr.addstr(int(y), int(x*2), str)


class enemy:
    max = 12        # 最初の敵の数
    l = [0]*40  # 敵が生きているかのフラグ
    x = [0]*40  # 敵ｘ座標
    y = [0]*40  # 敵ｙ座標
    c = "Ｏ"  # 敵キャラクタ

    def no():
        global gamestat
        f = 0
        for j in range(enemy.max):
            f += enemy.l[j]
        if f == 0:
            gamestat = 3
        return

    def move():
        dx = 0
        dy = 0
        tx = 0
        ty = 0

        for i in range(enemy.max):
            if enemy.l[i] == 0:  # 敵が死んでいたら、スキップ
                continue

            # プレイヤーを追いかけるように差分を求める
            dx = 0
            dy = 0
            if enemy.x[i] < player.x:
                dx = 1
            if enemy.x[i] > player.x:
                dx = -1
            if enemy.y[i] < player.y:
                dy = 1
            if enemy.y[i] > player.y:
                dy = -1

            #  今の位置の敵の姿を消す
            putchara(enemy.x[i], enemy.y[i], SPACE)

            #  敵の動いた先の座標を求める
            tx = enemy.x[i]+dx
            ty = enemy.y[i]+dy

            #
            #  敵同士の衝突のチェック
            #
            for j in range(enemy.max):
                if (i != j and enemy.l[j] == 1 and tx == enemy.x[j] and ty == enemy.y[j]):
                    enemy.l[i] = 0     # 敵が生きているフラグを降ろす。

            #
            #  岩との衝突チェック
            #
            for j in range(rock.max):
                if (rock.l[j] == 1 and tx == rock.x[j] and ty == rock.y[j]):
                    rock.l[j] = 0
                    enemy.l[i] = 0
                    putchara(tx, ty, SPACE)  # 岩、敵を消す

            enemy.x[i] = tx
            enemy.y[i] = ty
            if enemy.l[i] != 0:  # 生きているか？
                putchara(tx, ty, enemy.c)  # 敵を描画


class rock:
    max = 120     # 最初の岩の数
    l = [0]*320  # 岩があるかどうかのフラグ
    x = [0]*320  # 岩ｘ座標
    y = [0]*320  # 岩ｙ座標
    c = "＃"  # 岩キャラクタ


class player:
    x = 0         # プレイヤーx座標
    y = 0         # プレイヤーy座標
    c = "＠"      # プレイヤーキャラクタ

    def check_crash():
        global gamestat
        #
        # プレイヤーが何かに当たったかどうかの判断
        #
        for i in range(enemy.max):
            if (enemy.l[i] == 1 and player.x == enemy.x[i] and player.y == enemy.y[i]):
                gamestat = 1
        for i in range(rock.max):
            if (rock.l[i] == 1 and player.x == rock.x[i] and player.y == rock.y[i]):
                gamestat = 1
        return

    def move():
        while True:
            ch = stdscr.getkey()
            if ch == '7':
                dx = -1
                dy = -1
                break
            elif ch == '8':
                dx = 0
                dy = -1
                break
            elif ch == '9':
                dx = 1
                dy = -1
                break
            elif ch == 'u' or ch == '4':
                dx = -1
                dy = 0
                break
            elif ch == 'i' or ch == '5':
                dx = 0
                dy = 0
                break
            elif ch == 'o' or ch == '6':
                dx = 1
                dy = 0
                break
            elif ch == 'j' or ch == '1':
                dx = -1
                dy = 1
                break
            elif ch == 'k' or ch == '2':
                dx = 0
                dy = 1
                break
            elif ch == 'l' or ch == '3':
                dx = 1
                dy = 1
                break

    # プレイヤーが画面からはみ出ないか？
        px = player.x+dx
        py = player.y+dy
        if (px >= 0) and (px < xsize) and (py >= 0) and (py < ysize):
            putchara(player.x, player.y, SPACE)  # プレイヤーを一歩進ませる。
            player.x = px
            player.y = py
            putchara(px, py, player.c)


class game:
    #
    # ゲームの説明画面
    #
    def instruction():
        stdscr.clear()      # 画面のクリア

        stdscr.addstr("Maneaters Ver 1.3\n")
        stdscr.addstr("Mission : kill all maneaters to survive!\n")
        stdscr.addstr("Ｏ -- Maneater, chase player step by step.\n")
        stdscr.addstr("＃ -- Rock, die maneaters and player when touched. \n")
        stdscr.addstr("＠ -- Player, control for maneaters to crash to rock and survive!\n")
        stdscr.addstr("\n")
        stdscr.addstr("Key control:    　        Tenkey:     \n")
        stdscr.addstr(" ７  ８  ９            ７　８  ９   \n")
        stdscr.addstr("  ↖ ↑  ↗                 ↖ ↑  ↗     \n")
        stdscr.addstr("ｕ← ｉ →ｏ             ４← ５→ ６   \n")
        stdscr.addstr("  ↙ ↓  ↘                 ↙ ↓  ↘     \n")
        stdscr.addstr(" ｊ  ｋ   ｌ           １  ２  ３   \n")
        stdscr.addstr("\n")
        stdscr.addstr(" 'i' and '5' move maneaters and don't move player\n")
        stdscr.addstr("             Good Luck\n")
        stdscr.addstr("hit key\n")

        stdscr.getch()  # 一文字キー入力待ち。

    #
    # 初期化
    #
    def init():
        global gamestat
        stdscr.clear()        # 画面クリア

        gamestat = 0         # ゲーム状態を初期化

    #
    #  岩を置く。
    #  0番目はプレイヤーと敵との衝突回避のため、
    #  ダミーを画面中心に置く。そのため、岩の個数は、
    #  max-1となる。
    #
        for i in range(rock.max):
            a = random.randint(0, xsize-1)
            b = random.randint(0, ysize-1)
            rock.x[i] = a
            rock.y[i] = b
            putchara(a, b, rock.c)
            rock.l[i] = 1
        rock.x[0] = int(xsize/2)
        rock.y[0] = int(ysize/2)
        rock.l[0] = 0

    #  敵を岩とぶつからないように置く。
        i = 0
        while i < enemy.max:
            f = 0
            ex = random.randint(0, xsize-1)
            ey = random.randint(0, ysize-1)
            for j in range(rock.max):
                if (ex == rock.x[j] and ey == rock.y[j]):
                    f = f+1
            if f == 0:
                enemy.x[i] = ex
                enemy.y[i] = ey
                putchara(ex, ey, enemy.c)
                enemy.l[i] = 1
                i += 1

    # プレイヤーを画面中心に置く。
        player.x = xsize/2
        player.y = int(ysize/2)
        putchara(player.x, player.y, player.c)

    #
    #  ゲームの説明画面とメインのループ
    #
    def play():
        while True:
            game.instruction()

            game.init()  # ゲームの初期化

            game.main()  # ゲームのメイン処理

            if gamestat == 0:
                continue

            if gamestat == 1:  # プレイヤー負け
                putchara(player.x, player.y, CRASH)
                stdscr.addstr(0, 0, "You Lose. Game Over.")

            if gamestat == 3:  # プレイヤー勝ち
                stdscr.addstr(0, 0, "You Win! Game Over.")

            stdscr.refresh()
            if game.tryagainp() == 0:
                return   # リプレイしなければリターン。

    #
    #  ゲームのメイン処理
    #
    def main():
        while True:
            stdscr.refresh()

            player.move()
            player.check_crash()
            if gamestat != 0:
                return

            enemy.move()
            player.check_crash()
            if gamestat != 0:
                return

            enemy.no()
            if gamestat != 0:
                return

    def tryagainp():
        stdscr.addstr(1, 0, "Try Again? [y/n]")
        while True:
            ch = stdscr.getkey()
            if ch == 'n':
                return(0)
            if ch == 'y':
                return(1)


locale.setlocale(locale.LC_ALL, '')  # curses で、UTF-8 を使うために、
                                     # LC_ALLをセットする。

curses.noecho()  # 　キーボードエコーをなしにする
curses.curs_set(0)
game.play()             # ゲームを走らせる。
curses.endwin()         # 画面を閉じる。
exit(0)  # 終了。

```

# コンパイル

pyinstallerでコンパイルできます。以下のようなメッセージがでてきて、./dist以下にバイナリが出来ます。"maneaters"が実行ファイルです。

```
$ pyinstaller maneaters.py
511 INFO: PyInstaller: 3.6
511 INFO: Python: 3.8.2
559 INFO: Platform: Linux-5.4.0-47-generic-x86_64-with-glibc2.29
559 INFO: wrote /home/gar/maneaters/maneaters.spec
600 INFO: UPX is not available.
615 INFO: Extending PYTHONPATH with paths
['/home/gar/maneaters', '/home/gar/maneaters']
615 INFO: checking Analysis
615 INFO: Building Analysis because Analysis-00.toc is non existent
615 INFO: Initializing module dependency graph...
654 INFO: Caching module graph hooks...
678 INFO: Analyzing base_library.zip ...
5981 INFO: Processing pre-find module path hook   distutils
6009 INFO: distutils: retargeting to non-venv dir '/usr/lib/python3.8'
11391 INFO: Caching module dependency graph...
11574 INFO: running Analysis Analysis-00.toc
11624 INFO: Analyzing /home/gar/maneaters/maneaters.py
11843 INFO: Processing module hooks...
11843 INFO: Loading module hook "hook-_tkinter.py"...
12464 INFO: checking Tree
12464 INFO: Building Tree because Tree-00.toc is non existent
12464 INFO: Building Tree Tree-00.toc
12483 INFO: checking Tree
12483 INFO: Building Tree because Tree-01.toc is non existent
12483 INFO: Building Tree Tree-01.toc
12491 INFO: Loading module hook "hook-xml.etree.cElementTree.py"...
12520 INFO: Loading module hook "hook-sysconfig.py"...
12608 INFO: Loading module hook "hook-lib2to3.py"...
12635 INFO: Loading module hook "hook-pydoc.py"...
12650 INFO: Loading module hook "hook-distutils.py"...
12654 INFO: Loading module hook "hook-encodings.py"...
12731 INFO: Loading module hook "hook-xml.py"...
12911 INFO: Looking for ctypes DLLs
12964 INFO: Analyzing run-time hooks ...
12970 INFO: Including run-time hook 'pyi_rth__tkinter.py'
12993 INFO: Including run-time hook 'pyi_rth_multiprocessing.py'
13008 INFO: Looking for dynamic libraries
14161 INFO: Looking for eggs
14162 INFO: Python library not in binary dependencies. Doing additional searching...
14576 INFO: Using Python library /usr/lib/x86_64-linux-gnu/libpython3.8.so.1.0
14587 INFO: Warnings written to /home/gar/maneaters/build/maneaters/warn-maneaters.txt
14645 INFO: Graph cross-reference written to /home/gar/maneaters/build/maneaters/xref-maneaters.html
14680 INFO: checking PYZ
14680 INFO: Building PYZ because PYZ-00.toc is non existent
14680 INFO: Building PYZ (ZlibArchive) /home/gar/maneaters/build/maneaters/PYZ-00.pyz
15378 INFO: Building PYZ (ZlibArchive) /home/gar/maneaters/build/maneaters/PYZ-00.pyz completed successfully.
15385 INFO: checking PKG
15385 INFO: Building PKG because PKG-00.toc is non existent
15385 INFO: Building PKG (CArchive) PKG-00.pkg
15508 INFO: Building PKG (CArchive) PKG-00.pkg completed successfully.
15509 INFO: Bootloader /home/gar/.local/lib/python3.8/site-packages/PyInstaller/bootloader/Linux-64bit/run
15509 INFO: checking EXE
15509 INFO: Building EXE because EXE-00.toc is non existent
15510 INFO: Building EXE from EXE-00.toc
15538 INFO: Appending archive to ELF section in EXE /home/gar/maneaters/build/maneaters/maneaters
15579 INFO: Building EXE from EXE-00.toc completed successfully.
15582 INFO: checking COLLECT
15582 INFO: Building COLLECT because COLLECT-00.toc is non existent
15582 INFO: Building COLLECT COLLECT-00.toc
17116 INFO: Building COLLECT COLLECT-00.toc completed successfully.
$ 
```
