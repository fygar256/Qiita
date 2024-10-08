


**************************************************


Pythonとcursesで昔懐かしキャラクタゲームを再現してみた。


**************************************************


40年ほど前の、CBM-3032にあった、cursorという同人ソフトのキャラクタゲームをpythonでリライトしてみました。名前も忘れたので、「Asterisks」と、しました。
説明も、何もなしですが、動かすとゲームが始まります。
プレイヤーは「Ｏ」テンキーの１〜９の方向に動きます。
フルキーでも、「jkluio789」と、「↑↓←→」に、対応しています。
「q」キーで、いつでも抜けられます。

目的は、複数の敵、「＊」を、ブロック「B」で、八方を囲み、閉じ込めることです。
全ての敵を閉じ込めたら、「You Win」、敵に接触したら、「You Lose」となります。
デフォルト設計では、かなり易しめに作ってありますが、呼び出し時のオプションで、
敵の数とブロックの数を変えられます。

ubuntu pythonで、昔のゲームを作るポイントは５つ。
①cursesで、tty出力をする。
②locale.setlocale()で、cursesで、UTF-8を使えるようにしておく
③待ち時間なしのgetch()で、キー入力をする。
④time.sleep()で、全体の処理速度を落とす。
⑤仮想vram（ここでは、vvram[][])を使う。
です。

待ち時間なしのキー入力は、Qiitaの記事<a href="https://qiita.com/pukin/items/3b791b8b759dd704f765"> Pythonでキー入力待ちをしないキー入力</a>　を参考にしました。

pygameモジュールは使っていません。

```ast.py
#!/usr/bin/python3

import argparse
import curses
import random
import locale
import time
import select
import termios
import sys
import os
import fcntl

parser = argparse.ArgumentParser(
    description='Retro game \'Asterisks\'', add_help=True)
parser.add_argument('-b', help='number of blocks', type=int, default=400)
parser.add_argument('-e', help='number of enemies', type=int, default=5)
args = parser.parse_args()

asts = args.e
blocks = args.b


def getkey():
    fno = sys.stdin.fileno()
    attr_old = termios.tcgetattr(fno)
    attr = termios.tcgetattr(fno)
    attr[3] = attr[3] & ~termios.ECHO & ~termios.ICANON  # & ~termios.ISIG
    termios.tcsetattr(fno, termios.TCSADRAIN, attr)
    fcntl_old = fcntl.fcntl(fno, fcntl.F_GETFL)
    fcntl.fcntl(fno, fcntl.F_SETFL, fcntl_old | os.O_NONBLOCK)

    try:
        c = sys.stdin.read(3)
        if c=='^[0A': #ここで、^[はエスケープキーのコードを表します。私はviで入力したのですが、qiitaにコピペすると正しく表示されませんので、皆さん、エディタで^[をエスケープキーのコードに置き換えて下さい。
           c='8'
        elif c=='^[0B':
           c='k'
        elif c=='^[0D':
           c='u'
        elif c=='^[0C':
           c='o'
    finally:
        fcntl.fcntl(fno, fcntl.F_SETFL, fcntl_old)
        termios.tcsetattr(fno, termios.TCSANOW, attr_old)

    return c


vvram = [[' ' for i in range(24)] for j in range(40)]
astx = [0]*asts
asty = [0]*asts
playerx = 20
playery = 12

locale.setlocale(locale.LC_ALL, '')


def ps(x, y):
    vvram[x][y] = ' '
    stdscr.addch(y, x*2, '　')


def pp(x, y):
    vvram[x][y] = 'o'
    stdscr.addch(y, x*2, 'Ｏ')


def pb(x, y):
    vvram[x][y] = 'B'
    stdscr.addstr(y, x*2, 'Ｂ', curses.A_REVERSE)


def pw(x, y):
    vvram[x][y] = 'W'
    stdscr.addch(y, x*2, '＃')


def pa(x, y):
    vvram[x][y] = '*'
    stdscr.addch(y, x*2, '＊')


def initscreen():
    stdscr.clear()

    vvram = [[' ' for i in range(24)] for j in range(40)]

    wy = 0
    for wx in range(40):
        pw(wx, wy)
        pw(wx, wy+22)
    wx = 0
    for wy in range(22):
        pw(wx, wy)
        pw(wx+39, wy)

    for block in range(blocks):
        x = random.randint(1, 38)
        y = random.randint(1, 21)
        pb(x, y)

    pp(playerx, playery)

    for a in range(asts):
        while True:
            x = random.randint(1, 38)
            y = random.randint(1, 21)
            if vvram[x][y] == ' ':
                break
        pa(x, y)
        astx[a] = x
        asty[a] = y
    stdscr.refresh()


def move_ast():
    for i in range(asts):
        x = astx[i]+random.randint(-1, 1)
        y = asty[i]+random.randint(-1, 1)
        c = vvram[x][y]
        if c == ' ' or c == 'o':
            ps(astx[i], asty[i])
            astx[i] = x
            asty[i] = y
            pa(x, y)


def move_blocks(dx, dy):
    x = playerx+dx
    y = playery+dy
    while True:
        x += dx
        y += dy
        c = vvram[x][y]
        if (c != 'B'):
            break
    if c == ' ':
        pb(x, y)
        return(' ')
    return('W')


def move_player(k):
    global playerx, playery
    if k == '7':
        dx = -1
        dy = -1
    elif k == '8':
        dx = 0
        dy = -1
    elif k == '9':
        dx = 1
        dy = -1
    elif k == 'u' or k == '4':
        dx = -1
        dy = 0
    elif k == 'o' or k == '6':
        dx = 1
        dy = 0
    elif k == 'j' or k == '1':
        dx = -1
        dy = 1
    elif k == 'k' or k == '2':
        dx = 0
        dy = 1
    elif k == 'l' or k == '3':
        dx = 1
        dy = 1
    else:
        dx = 0
        dy = 0
    c = vvram[playerx+dx][playery+dy]
    if (c == 'B'):
        c = move_blocks(dx, dy)
    if (c == ' ' or c == 'B'):
        ps(playerx, playery)
        playerx += dx
        playery += dy
        pp(playerx, playery)


def lose_check():
    f = 0
    for i in range(asts):
        f += 1 if (astx[i] == playerx and asty[i] == playery) else 0
    return(f)


def game_finish():
    for i in range(asts):
        if (vvram[astx[i]-1][asty[i]-1] == ' '
            or vvram[astx[i]][asty[i]-1] == ' '
            or vvram[astx[i]+1][asty[i]-1] == ' '
            or vvram[astx[i]-1][asty[i]] == ' '
            or vvram[astx[i]+1][asty[i]] == ' '
            or vvram[astx[i]-1][asty[i]+1] == ' '
            or vvram[astx[i]][asty[i]+1] == ' '
                or vvram[astx[i]+1][asty[i]+1] == ' '):
            return(0)
    return(1)


def mainloop():
    x = 0
    while True:
        k = getkey()
        if k == 'q':
            return
        move_player(k)
        if x % 4 == 0:
            move_ast()
        g = 1
        if (lose_check()):
            stdscr.addstr(0, 0, "You Lose!! Game Over.")
            g = 0
        if (game_finish()):
            stdscr.addstr(0, 0, "You Win!! Game Over.")
            g = 0
        if g == 0:
            stdscr.refresh()
            while True:
                k = getkey()
                if k == 'q':
                    return
        stdscr.refresh()

        time.sleep(0.1)
        x += 1


stdscr = curses.initscr()
stdscr.keypad(True)
curses.curs_set(0)
initscreen()
mainloop()
curses.endwin()

```
