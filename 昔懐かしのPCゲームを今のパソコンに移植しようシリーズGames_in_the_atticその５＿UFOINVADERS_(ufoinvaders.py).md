


**************************************************


昔懐かしのPCゲームを今のパソコンに移植しようシリーズGames in the atticその５　UFOINVADERS (ufoinvaders.py)


**************************************************



ufoinvaders.pyは実行ファイルです。
supertext.pyというモジュールを使っています。

https://qiita.com/fygar256/items/11b92b7285359f600b36



上から侵略してくる敵を砲弾で撃破し、画面最下段のラインにまで来ないようにするゲームです。65体の敵を倒したらミッションクリア、敵が画面最下段にまで到着したらゲームオーバーです。

オリジナルは、42年前のCommodoleのCBM3032にあったゲームで、正式名称は忘れたましたが、何体敵を倒したら終わりということはなかったと思います。同人誌CURSORのゲームでした。記憶があやふやなもので、思い出せません。

キー操作は、'space'で砲撃、'4'でキャノンが左へ移動、'6'でキャノンが右に移動します。'q'でゲームを抜けます。

すごく単純なゲームです。敵の撃破数が出ますので、スコアを競ってください。

```ufoinvaders.py
#!/usr/bin/python3
import supertext as st
import random

HITMAX=65

counter=0
number_of_enemies=2
number_of_hits=0
wait=2
skipratio=0
enemy1=chr(0x95)+chr(0x90)+chr(0x92)
enemy2=chr(0x93)+chr(0x96)+chr(0x94)
canon1=" "+chr(0x85)+chr(0x84)+chr(0x86)+" "
canon2=" "+chr(0x91)+chr(0x91)+chr(0x91)+" "
bulletchar=chr(0x82)
canonx=18
canony=23
epos=[]
bx=0
by=0

def enemies():
    global epos
    while(len(epos)<number_of_enemies):
        epos+=[[random.randrange(0,36)+1,0]]
    for i in range(len(epos)):
        x=epos[i][0]
        y=epos[i][1]
        st.color((255,255,255))
        st.locate(x,y)
        st.putstr("   ")
        st.locate(x,y+1)
        st.putstr(enemy1)
        st.locate(x,y+2)
        st.putstr(enemy2)
        epos[i]=[x,y+1]
        
def bullet():
    global bx,by
    if bx==0:
        if st.getkey('space'):
            bx=canonx+2
            by=canony
        else:
            return
    st.color((255,255,0))
    st.locate(bx,by)
    st.putchar(' ')
    by-=1
    if by==0:
        bx=0
    st.locate(bx,by)
    st.putchar(bulletchar)

def hit():
    global epos,bx,by,number_of_hits
    if bx==0:
        return number_of_hits
    l=[]
    for e in epos:
        (x,y)=(e[0],e[1])
        if (x==bx or x+1==bx or x+2==bx) and (y+1==by or y==by):
            number_of_hits+=1
            st.locate(x,y)
            st.putstr('   ')
            st.locate(x,y+1)
            st.putstr('   ')
            bx=0
        else:
            l+=[e]
    epos=l
    return number_of_hits

def invaded():
    for e in epos:
        if e[1]==23:
            return 1
    return 0

def canon():
    global canonx
    st.locate(canonx,canony)
    st.color((0,255,255))
    st.putstr(canon1)
    st.locate(canonx,canony+1)
    st.putstr(canon2)
    if st.getkey('4'):
        canonx-=1 if canonx>0 else 0
    elif st.getkey('6'):
        canonx+=1 if canonx<35 else 0

def disp_scores():
    st.locate(0,0)
    st.color((0,255,0))
    st.putstr("THE UFO INVADERS ")
    st.color((255,255,0))
    st.putstr("NUMBER OF HITS: ")
    s=str(number_of_hits)
    st.color((255,255,255))
    st.putstr(s)

def difficulty(n):
    global wait,skipratio,number_of_enemies
    if n<20:
        wait=1.7
        number_of_enemies=3
        skipratio=8
    elif 20<=n<40:
        wait=1.5
        number_of_enemies=4
        skipratio=7
    elif 40<=n<60:
        wait=1.0
        number_of_enemies=5
        skipratio=6
    elif 60<=n<80:
        skipratio=5
        wait=0.5
        number_of_enemies=6
    elif 80<=n<100:
        skipratio=4
        wait=0.3
        number_of_enemies=7
    else:
        skipratio=1
        wait=0
        number_of_enemies=8

def main():
    global number_of_enemies,wait,counter
    st.setscreen("UFO INVADERS")
    while(1):
        difficulty(number_of_hits)
        canon()
        bullet()
        counter+=1
        if not counter%skipratio:
            enemies()
        h=hit()
        disp_scores()

        if h==HITMAX:
            while(not st.getkey('q')):
                st.color((0,255,0))
                st.locate(13,12)
                s="YOU HIT "+str(number_of_hits)+" UFOS"
                st.putstr(s)
                st.color((255,0,0))
                st.locate(13,13)
                st.putstr("MISSION CLEARED")
                st.refresh()
                st.color((0,255,255))
                st.locate(15,14)
                st.putstr("HIT 'q' KEY")
            return

        if invaded():
            while(not st.getkey('q')):
                st.color((0,255,0))
                st.locate(8,12)
                st.putstr("THE UFOS HAVE INVADED")
                st.color((255,0,0))
                st.locate(15,13)
                st.putstr("GAME OVER")
                st.color((0,255,255))
                st.locate(14,14)
                st.putstr("HIT 'q' KEY")
                st.refresh()
            return

        if st.getkey('q'):
            exit(1)
        st.refresh()
        st.sleep(wait)

if __name__=="__main__":
    main()
    exit(0)

```

