


**************************************************


GAMES in the attic 昔懐かしのPCゲームを今のパソコンに移植しよう　SUPERTEXT python用console module　Ver. 1.1


**************************************************


supertext.pyは、昔の40x25ディスプレイのパソコンのキャラクタゲームを移植する
ためのコンソール用pythonモジュールです。スクリーンに8＊8ドットのオリジナルキャラクタを表示できます。

cursesでは、当時のグラフィックキャラクタを再現できないため、キャラクタエディタと共に作りました。
supertextは、カレントディレクトリからchars.txtというキャラクタパターンデータを読み込みます。chars.txtのある場所は、プログラム内の、path_to_charsという変数を変えることによって変更することができます。最初は、/home/gar/lib/chars.txtになっていますが、使用者個人の環境に合せ、任意のパスに変更してください。
$ ./supertext.pyで、画面上に使えるキャラクタコードテーブルを表示します。キャラクタは0x00~0xffまでの256種類あり、charactereditor.pyで編集できます。

supertext.pyは実行すると、扱えるキャラクタのアスキーコード表を表示します。

メソッド：
setscreen(title) Supertextと画面を初期化します。このとき、
                キャラクタパターンをファイルから読み込みます。

refresh()       画面をリフレッシュします。
                リフレッシュするまで画面に変更を行っても表示されません。
quitsupertext() supertextから抜けます

locate(x,y)     カーソル位置を(x,y)にセットする
color((r,g,b))  文字の色をセットする
bgcolor((r,g,b)) 背景色をセットする

putstr(str)     文字列strを出力する。
putchar(char)   キャラクタcharを出力する。
scrollup(x,y,w,h) 画面(x,y)の位置から始まる幅w、高さhのウィンドウをスクロールアップする。
scrolldown(x,y,w,h) 画面(x,y)の位置から始まる幅w、高さhのウィンドウをスクロールダウンする。

getkey(k)       キー入力を取得する。普通のgetkeyと違うところは、
                これはある特定のキーが押されているかどうかを返す関数であること
                です。返り値は1か0で、キーkが押下されていれば1を返す。
                例えば、while(getkey('q')):で、'q'が押されるまでループします。

getkeys()       キーボードの状態を取得する。返り値は、{ 'k':1,'e':0,'y':0 }
                のような辞書で、上の例は、キーボードの'k'が押下されている状態を
                示す。'e','y'のキーと他のキーは押下されていない。

clearscreen()   スクリーンと仮想VRAMを消去する

peek(x,y)       仮想VRAMの位置(x,y)におけるキャラクタコードを返す
poke(x,y,c)     仮想VRAMの位置(x,y)にコードcを書き込み、画面上の位置(x,y)に
                文字cを出力する。

sleep(t)        tの間スリープする。tは0~3位の適当な値。(float)
                sleepは、スリープしている間もキー入力を受け付け、
                キーイベントの取零しやキーリピートによる不具合の発生を抑えます。

仮想VRAMは画面をクリアするとchr(0)で埋め尽くされ、文字を出力すると(x,y)の位置の仮想VRAMの値がその文字になります。但し、' '(Space)を出力すると、その位置の仮想VRAMの値がchr(0)になります。あと、特殊なコードは改行で、'\n'を出力すると、改行し、
カーソル位置だけが変わります。

```supertext.py
#!/usr/bin/python3
import pygame
import sys
import os
import time
import numpy as np
from pygame.locals import *

curx=0
cury=0
charsize=8
dotscale=2
XSIZE=40
YSIZE=25
color_=(255,255,255)
bgcolor_=(0,0,0)
vvram=[[chr(0) for _ in range(25)] for __ in range(40)]
keys={ 'space':0,'escape':0,'return':0 }
path_to_chars='/home/gar/lib/chars.txt'

screen=[]
spacechar=[ 0 for _ in range(8*8) ]
chars=[ spacechar for _ in range(0,256) ]

def loadchars():
    global chars
    chars=np.loadtxt(path_to_chars)

def bgcolor(c):
    global bgcolor_
    bgcolor_=c

def color(c):
    global color_
    color_=c

def locate(x,y):
    global curx,cury
    curx=x
    cury=y

def scrollup(sx,sy,width,height):
    global screen
    px=sx*dotscale*charsize
    py=sy*dotscale*charsize
    w=width*dotscale*charsize
    h=(height-1)*dotscale*charsize
    for i in range(h):
        for j in range(w):
            screen.set_at((px+j,py+i),screen.get_at((px+j,py+i+dotscale*charsize)))

    for i in range(height-1):
        for j in range(width):
            vvram[sx+j][sy+i]=vvram[sx+j][sy+i+1]
    locate(sx,sy+height-1)
    s=' '*width
    putstr(s)
    refresh()

def scrolldown(sx,sy,width,height):
    global screen
    w=width*dotscale*charsize
    h=height*dotscale*charsize
    px=sx*dotscale*charsize
    py=sy*dotscale*charsize
    for i in range(h-1,dotscale*charsize-1,-1):
        for j in range(w):
            screen.set_at((px+j,py+i),screen.get_at((px+j,py+i-dotscale*charsize)))

    for i in range(height-1,0,-1):
        for j in range(width):
            vvram[sx+j][sy+i]=vvram[sx+j][sy+i-1]
    locate(sx,sy)
    s=' '*width
    putstr(s)
    refresh()

def putchar(ch):
    global screen,curx,cury
    if ch=='\n':
        curx=0
        cury+=1
        if cury>=YSIZE:
            cury-=1
        return

    px=curx*dotscale*charsize
    py=cury*dotscale*charsize
    for i in range(8*8):
            c=color_ if chars[ord(ch)][i] else bgcolor_
            screen.fill(c,(px+i%8*dotscale,py+i//8*dotscale,dotscale,dotscale))
    if ch==' ':
        vvram[curx][cury]=chr(0)
    else:
        vvram[curx][cury]=ch
    move_curpos()

def move_curpos():
    global curx,cury
    curx+=1
    if curx>=XSIZE:
        curx=0
        cury+=1
        if cury>=YSIZE:
            cury-=1
            curx=XSIZE-1

def putstr(s):
    for ch in s:
        putchar(ch)

def peek(x,y):
    ch=vvram[x][y]
    return ch

def poke(x,y,c):
    global curx,cury
    sx=curx
    sy=cury
    locate(x,y)
    putchar(c)
    curx=sx
    cury=sy
    return

def clearscreen():
    global vvram,screen
    screen.fill((0,0,0)) # clear screen
    vvram=[[chr(0) for _ in range(25)] for __ in range(40)]
    return

def getkeys():
    getkeyboard()
    return keys

def getkey(k):
    if getkeys()[k]:
        return 1
    return 0

def getkeyboard():
    for event in pygame.event.get():
        if event.type == QUIT:
            sys.exit(0)
        if event.type == KEYDOWN:  # キーを押したとき
            k=pygame.key.name(event.key)
            if len(k)==3 and k[1] in "0123456789.+-*/":
                k=k[1]
            keys[k]=1
            return k
        if event.type == KEYUP:    # キーを離したとき
            k=pygame.key.name(event.key)
            if len(k)==3 and k[1] in "0123456789.+-*/":
                k=k[1]
            keys[k]=0
            return ''
    return ''

def setscreen(title):
    global screen,keys
    pygame.init()    # Pygameを初期化
    screen = pygame.display.set_mode((XSIZE*charsize*dotscale,YSIZE*charsize*dotscale))    # 画面を作成
    pygame.display.set_caption(title)    # タイトルを作成
    loadchars()
    clearscreen()
    color((255,255,255))
    bgcolor((0,0,0))
    for _ in range(256):
        keys[chr(_)]=0
    return

def sleep(t):
    if (t<0.0001):
        getkeyboard()
        time.sleep(0.0001)
        return
    y=int(t/0.005)
    for i in range(y):
        time.sleep(0.0001)
        getkeyboard()

def quitsupertext():
    pygame.quit()

def refresh():
    pygame.display.update()

def main():
    setscreen("Supertext")
    color((0,255,0))
    putstr(" 0123456789ABCDEF\n")
    for i in range(0,16):
        color((0,255,0))
        locate(0,i+1)
        putchar(chr(0x30+i if i<10 else 0x41+i-10))
        color((255,255,255))
        for j in range(0,16):
            c=i*16+j
            locate(j+1,i+1)
            putchar(chr(c))

    locate(5,20)
    color((0,255,255))
    putstr("SUPERTEXT CODE and CHARACTERS")
    locate(5,21)
    color((0,0,255))
    putstr("Press 'q' key to quit.")
        
    refresh()

    while 1:
        if getkey('8'):
            scrollup(0,0,40,25)
        if getkey('2'):
            scrolldown(0,0,40,25)
        k=getkey('q')
        if k==1:
            quitsupertext()
            sys.exit()

if __name__=='__main__':
    main()

```

### supertext用キャラクタエディタcharactereditor.py

Supertextのキャラクタファイル、chars.txtを編集します。
一つのキャラクタは8x8ドットからなります。
charactereditor.pyは実行時に`charactereditor.py </path/to/chars.txt>として与えられたchars.txtを扱います。

lで、キャラクタバッファから画面にキャラクタパターンをロードします。
sで、画面のキャラクタパターンをキャラクタバッファにセーブします。
wで、キャラクタバッファをchars.txtにセーブします。
cで、画面をクリアします。
qで、プログラムから抜けます。
画面をクリックするとドットを打ったり消したりします。

コマンドを入力してもフォーカスしているウィンドウは変わらないので、マウスでクリックしてフォーカスするウィンドウを変えて下さい。
```charactereditor.py
#!/usr/bin/python3
from pygame.locals import *
from scipy import signal
import pygame
import sys
import numpy as np
import os
import binascii



scale=24
height=8
width=8
chars=[[0 for i in range(8*8)] for k in range(256)]
charsfn=""

def draw_grid(screen):
  screen.fill((0,0,0))
  for i in range(width):
    pygame.draw.line(screen,(0,255,0),(0,i*scale),(scale*width,i*scale),1)
  for j in range(height):
    pygame.draw.line(screen,(0,255,0),(j*scale,0),(j*scale,scale*height),1)

def set(screen,cx,cy,c):
  screen.fill((c*255,c*255,c*255),(cx*scale+1,cy*scale+1,scale-1,scale-1))

def put(F,screen):
  for i in range(len(F)):
    for j in range(len(F[1])):
       set(screen,j,i,F[i][j])

def rev(F,cx,cy):
  F[cy][cx]=int(F[cy][cx])^1
  return(F)

def clear():
    return(np.zeros((height,width)))

def input_code():
    s=input("input character or hexadecimal code w/o 0x:")
    if len(s)==2:
        ch=int(s,16)
    else:
        ch=ord(s[0])
    return ch

def load_char(screen):
    ch=input_code()
    F=[]
    a=0
    for i in range(8):
        G=[]
        for j in range(8):
            G+=[chars[ch][a]]
            a+=1
        F+=[G]
    put(F,screen)
    return(F)

def save_char(F):
    ch=input_code()
    a=0
    for i in range(8):
        for j in range(8):
             chars[ch][a]=F[i][j]
             a+=1
def eventloop(F,screen):
    global chars
    run='e'
    while run!='q':
        for event in pygame.event.get():
            if event.type == QUIT:
                run='q'
            if event.type == KEYDOWN:  # キーを押したとき
                k=pygame.key.name(event.key)
                if k== 'q':
                    run='q'
                elif k=='l':
                    F=load_char(screen)
                elif k=='w':
                    print(f"file {charsfn} wrote.")
                    print(np.savetxt(charsfn, chars, "%d"))
                elif k=='s':
                    save_char(F)
                elif k=='c':
                    F=clear()
                    put(F,screen)
            elif event.type == MOUSEBUTTONDOWN:
                    x, y = event.pos
                    F=rev(F,x//scale,y//scale)
                    put(F,screen)
            elif event.type == MOUSEMOTION:
                    x, y = event.pos
        pygame.display.update()

def main():
    global chars,charsfn
    pygame.init()    # Pygameを初期化
    screen = pygame.display.set_mode((scale*width,scale*height))    # 画面を作成
    pygame.display.set_caption("pattern editor")    # タイトルを作成
    draw_grid(screen)
    charsfn=sys.argv[1] if len(sys.argv)>=2 else "chars.txt"
    if os.path.exists(charsfn):
         chars = np.loadtxt(charsfn)
    F=clear()
    put(F,screen)
    eventloop(F,screen)
    pygame.quit()
    sys.exit()

if __name__=='__main__':
    main()

```

### supertext用キャラクタパターンファイル chars.txt

https://github.com/fygar256/supertext/blob/main/chars.txt
