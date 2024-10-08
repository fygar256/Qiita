


**************************************************


GENERAL PATTERN EDITOR patterneditor.py


**************************************************


自由サイズの汎用2値画面パターンエディタです。

patterneditor.pyは実行ファイルです。

0 0 1 1 1 0 0 0
0 1 1 0 1 1 0 0
1 1 0 0 0 1 1 0
1 1 0 0 0 1 1 0
1 1 1 1 1 1 1 0
1 1 0 0 0 1 1 0
1 1 0 0 0 1 1 0
0 0 0 0 0 0 0 0

のような0 と 1からなるテキストファイルを生成します。
import numpy as np
F = np.loadtxt(a)
として、numpyでファイルを読み込み、2次元のリストにすることができます。

'l'で   画面の幅、高さをファイルの要素数に合わせ、ファイルからパターンをロード
        します。
's'で   現在の画面をファイルにセーブします。
'z'で   現在の画面の幅と高さの要素数を設定します。
'c'で   現在の画面をクリアします。
'q'で   終了です。
画面のセルをマウスでクリックしたら、On/Offします。最初の画面の要素数は8x8です。

```patterneditor.py
#!/usr/bin/python3
from pygame.locals import *
from scipy import signal
import pygame
import sys
import numpy as np
import os
import binascii

width=8
height=8
scale=16

def draw_grid(screen):
  screen.fill((0,0,0))
  for i in range(width):
    pygame.draw.line(screen,(0,255,0),(i*scale,0),(i*scale,scale*height),1)
  for i in range(height):
    pygame.draw.line(screen,(0,255,0),(0,i*scale),(scale*width,i*scale),1)

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

def setscr():
    pygame.quit()
    pygame.init()    # Pygameを初期化
    screen = pygame.display.set_mode((scale*width,scale*height))   # 画面を作成
    s=str(width)+"x"+str(height)+" pattern editor"
    pygame.display.set_caption(s)    # タイトルを作成
    draw_grid(screen)
    return screen

def eventloop(F,screen):
    global width,height
    run='e'
    while run!='q':
        for event in pygame.event.get():
            if event.type == QUIT:
                run='q'
            if event.type == KEYDOWN:  # キーを押したとき
                k=pygame.key.name(event.key)
                if k== 'q':
                    run='q'
                elif k=='z':
                    width=int(input("Input width:"))
                    height=int(input("Input height:"))
                    screen=setscr()
                    F=clear()
                elif k=='l':
                    a=input("Filename:")
                    if os.path.exists(a):
                        F = np.loadtxt(a)
                        height=len(F)
                        width=len(F[0])
                        screen=setscr()
                        put(F,screen)
                elif k=='s':
                    a=input("Filename:")
                    print(np.savetxt(a, F, "%d"))
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
    screen=setscr()
    F=clear()
    put(F,screen)
    eventloop(F,screen)
    pygame.quit()
    sys.exit()

if __name__=='__main__':
    main()
```
