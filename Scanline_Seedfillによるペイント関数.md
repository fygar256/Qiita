


**************************************************


Scanline Seedfillによるペイント関数


**************************************************


Scanline Seedfillによるペイント関数です。
指定された背景色の部分を指定された色で塗りつぶします。
python3で書かれています。グラフィックスの部分はpygameを使っています。

```scanline.py
#!/usr/bin/python3
from pygame.locals import *
import pygame
import sys
import os
import random
MAXX=399
MAXY=399
buff=[]
pygame.init()    # Pygameを初期化
screen = pygame.display.set_mode((MAXX+1,MAXY+1))    # 画面を作成
pygame.display.set_caption("paint(scanline seedfill)" )    # タイトルを作成

def point(p):
    return(screen.get_at(p)[:3])

def scanline(lx,rx,y,bc):
  global buff
  while(lx<=rx):
    while (lx<rx):
      if point((lx,y))==bc: break
      lx+=1
    if point((lx,y))!=bc: break
    while(lx<=rx):
      if point((lx,y))!=bc: break
      lx+=1
    buff.append((lx-1,y))

def paint(p,bc,pc):
  global buff
  col=point(p)
  if col != bc:
      return
  buff=[p]
  while buff:
    (p,buff)=(buff[0],buff[1:])
    (lx,y)=p
    rx=lx

    if point(p)!=bc:
      continue

    while ( rx<MAXX ):
      if point((rx+1,y))!=bc: break
      rx=rx+1

    while ( lx>0 ):
      if point((lx-1,y))!=bc: break
      lx=lx-1

    for i in range(lx,rx+1):
      screen.set_at((i,y),pc)

    if y-1 >= 0:
      scanline(lx,rx,y-1,bc)
    if y+1 <= MAXY:
      scanline(lx,rx,y+1,bc)

def waitkey():
    while True:
        for event in pygame.event.get():
            if event.type == QUIT:
                sys.exit(0)
            if event.type == KEYDOWN:  # キーを押したとき
                k=pygame.key.name(event.key)
                return(k)

def main():
    screen.fill((0,0,0)) # clear screen

    for x in range(120):
      screen.fill((255,255,255),(random.randrange(0,400),random.randrange(0,400),50,50))
    pygame.display.update()
    bc=screen.get_at((3,4))[:3] # (3,4)における背景色の取得
    paint((3,4),bc,(0,255,0)) # (3,4)から背景色bcで、緑(0,255,0)をペイント
    pygame.display.update()
    key=waitkey()
    pygame.quit()
    sys.exit()

if __name__=='__main__':
    main()
```
