


**************************************************


DDAを使った円の描画　python


**************************************************


DDA（デジタル微分解析器）を使った円の描画（基礎的図形処理）のコードです。
Arch Linuxで動作確認しました。
グラフィックスのプラットフォームはpygameを使っています。
シフトと加減算のみを使った整数演算の円描画アルゴリズムですので、低性能CPUのアセンブリ言語に落とす時、便利です。

```ddacircle.py
#!/usr/bin/python3
from pygame.locals import *
import pygame
import sys
import os
from pygame.locals import *

def waitkey():
    while True:
        for event in pygame.event.get():
            if event.type == QUIT:
                sys.exit(0)
            if event.type == KEYDOWN:  # キーを押したとき
                k=pygame.key.name(event.key)
                return(k)


def draw_circle(screen,cx,cy,r):
  x=r
  y=0

# ブレゼンハムの円アルゴリズムによる円の描画
# 比較、加算、減算による座標計算
  while ( x >= y ):
    set(screen,( y+cx, x+cy))  # 0-45°の間
    set(screen,( x+cx, y+cy))  # 45-90°の間
    set(screen,(-x+cx, y+cy))  # 90-135°の間
    set(screen,(-y+cx, x+cy))  # 135-180°の間
    set(screen,(-y+cx,-x+cy))  # 180-225°の間
    set(screen,(-x+cx,-y+cy))  # 225-270の間
    set(screen,( x+cx,-y+cy))  # 270-315の間
    set(screen,( y+cx,-x+cy))  # 315-360の間
    y+=1
    r -= y+y
    if r < 1:
      x-=1
      r+=x+x

def set(screen,p):
  screen.set_at(p,(255,255,255))

def main():
    pygame.init()    # Pygameを初期化
    screen = pygame.display.set_mode((200,200))        # 200x200の画面を作成
    pygame.display.set_caption("DDAによる円の描画")    # タイトルを作成
    screen.fill((0,0,0)) # clear screen

    draw_circle(screen,100,100,50)

    pygame.display.update()
    key=waitkey()
    pygame.quit()
    sys.exit()

if __name__=='__main__':
    main()

```
