


**************************************************


DDAによる線分描画


**************************************************



DDA（デジタル微分解析器）による線分の描画をします。
整数演算のみで描画ができるので、アセンブラに落とすときなど便利です。
Pythonで書かれています。グラフィックス・キー入力はpygameを使っています。

```line.py
#!/usr/bin/python3
from pygame.locals import *
import pygame
import sys
import os

def waitkey():
    while True:
        for event in pygame.event.get():
            if event.type == QUIT:
                sys.exit(0)
            if event.type == KEYDOWN:  # キーを押したとき
                k=pygame.key.name(event.key)
                return(k)


def draw_line(screen,p1,p2):
  (x1,y1)=p1
  (x2,y2)=p2
  (dx,sx) =(x2-x1,1) if x2>x1 else (x1-x2,-1)
  (dy,sy) =(y2-y1,1) if y2>y1 else (y1-y2,-1)
  if (dx>=dy):
    e = -dx
    for i in range( dx+1 ):
      set( screen,(x1,y1) )
      x1 -= sx
      e += dy+dy
      if ( e>=0 ):
        y1 -= sy
        e -= dx+dx
  else:
    e = -dy
    for i in range( dy+1 ):
      set( screen,(x1,y1) )
      y1 -= sy
      e+= dx+dx
      if ( e>=0 ):
        x1 -=sx
        e -=dy+dy

def set(screen,p):
  screen.set_at(p,(255,255,255))

def main():
    pygame.init()    # Pygameを初期化
    screen = pygame.display.set_mode((200,200))    # 200x200の画面を作成
    pygame.display.set_caption("DDAによる線分描画")    # タイトルを作成
    screen.fill((0,0,0)) # clear screen

    for x in range(0,205,5):
        draw_line(screen,(100,100),(x,0))
        draw_line(screen,(100,100),(x,200))
    for y in range(0,205,5):
        draw_line(screen,(100,100),(0,y),)
        draw_line(screen,(100,100),(200,y))

    pygame.display.update()
    key=waitkey()
    pygame.quit()
    sys.exit()

if __name__=='__main__':
    main()

```
