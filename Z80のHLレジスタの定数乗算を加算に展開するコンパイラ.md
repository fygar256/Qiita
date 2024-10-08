


**************************************************


Z80のHLレジスタの定数乗算を加算に展開するコンパイラ


**************************************************


Z80のHLレジスタの定数乗算を加算命令に展開するコンパイラです。
2の補数を扱っているので、乗数や被乗数が負の数のときでも使えます。
但し、signed 16bitで表される、オーバーフローしない範囲での演算が正常です。

C版　コンパイル　`cc z80constmul.c -o z80constmul`

使用例 `z80constmul <被乗数> <乗数>`

2022/12 Ver2.0　バグがあったので、ソースを訂正しました。
2023/1 ver 2.1 ちょっと改良、最適化。

2024/8/30 ver 2.1 python version

# C バージョン

```z80constmul.c
#include  <stdio.h>
#include  <stdlib.h>
// z80 compiler for expading constant multiplication of HL register to addition.
// Z80 HL*定数を加算命令に展開するコンパイラ Ver 2.1
// 乗数,被乗数は2の補数を扱っているので、負数も可
//
int main(int argc, char* argv[])
{
    short hl,de,l;
    int b=0;

    // 引数（乗数の取得）
    if (argc!=3) fprintf(stderr,"usage:z80constmul <被乗数> <乗数>\n"),exit(1);
    hl=(short) atoi(argv[1]);
    l=(short) atoi(argv[2]);

    // 乗数が何ビットあるか計算 (b=bit数となる)
    for(ushort r=(ushort)l;r;r>>=1 ) b++;

    // コンパイル（ヘッダ）
    if (l==0) printf("ld hl,0\n"),hl=0;
    else  {
      printf("ld hl,%d ; 被乗数 この行を省けばhlの値は何でもいい。\n",(int)hl);
      printf("ld d,h\n");
      printf("ld e,l\n");
      de=hl;

      // コンパイル（展開）
      for (int i=b-1;i>=0 ; i--) {
        if ( (((l>>i) & 1)!=0 ) && (i!=b-1))
          printf("add hl,de\n"),hl += de;
        if (i!=0)
          printf("add hl,hl\n"),hl += hl;
      }
    }

    printf(";hl=%d\n",(int) hl);
    exit(0);
}

```

# python バージョン

```z80constmul.py
#!/usr/bin/python3
import sys
#
# Z80 HL*定数を加算命令に展開するコンパイラ Ver 2.1
# ２の補数を扱っているので、被乗数、乗数共に負数も可
# 

# 何ビットあるか計算 (b=bit数となる)
def nbit(l):
    b=0
    r=l
    while(r):
        r>>=1
        b+=1
    return b

# 2の補数表示
def print2complement(i):
    print(f"{i if i<0x8000 else i-65536}",end="")

# expand
def expand(hl,l):
    hl=hl&0xffff
    l=l&0xffff
    b=nbit(l)

    # ヘッダ
    if l==0:
        print("ld hl,0")
        hl=0
        return hl

    print("ld hl,",end='')
    print2complement(hl)
    print("; 被乗数 この行を省けばhlの値は何でもいい。");
    print("ld d,h");
    print("ld e,l");
    de=hl;

    # 展開
    for i in range(b-1,-1,-1):
        if l>>i & 1 and i!=b-1:
            print("add hl,de")
            hl = (hl+de)&0xffff
        if i:
            print("add hl,hl")
            hl = (hl+hl)&0xffff
    return hl

# main
def main(hl,l):
    hl=expand(hl,l)
    print(f";hl=",end="")
    print2complement(hl)
    print("")
    return

if __name__=="__main__":
    if len(sys.argv)!=3:
        print("Usage:z80constmul.py <被乗数> <乗数>")
        exit(1)
    main(int(sys.argv[1]),int(sys.argv[2]))
    exit(0)
```

実行例

```
$ z80constmul 100 0
ld hl,0
;hl=0
$ z80constmul 100 1
ld hl,100 ; 被乗数 この行を省けばhlの値は何でもいい。
ld d,h
ld e,l
;hl=100
$ z80constmul 100 20
ld hl,100 ; 被乗数 この行を省けばhlの値は何でもいい。
ld dprintf("ld de,hl\n");,h
ld e,l
add hl,hl
add hl,hl
add hl,de
add hl,hl
add hl,hl
;hl=2000
$ z80constmul 100 -1
ld hl,100 ; 被乗数 この行を省けばhlの値は何でもいい。
ld d,h
ld e,l
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
;hl=-100
$ z80constmul 100 -327
ld hl,100 ; 被乗数 この行を省けばhlの値は何でもいい。
ld d,h
ld e,l
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,hl
add hl,de
add hl,hl
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,hl
add hl,hl
add hl,de
;hl=-32700
$ z80constmul 0 3
ld hl,0 ; 被乗数 この行を省けばhlの値は何でもいい。
ld d,h
ld e,l
add hl,hl
add hl,de
;hl=0
$ z80constmul -3 -3
ld hl,-3 ; 被乗数 この行を省けばhlの値は何でもいい。
ld d,h
ld e,l
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,de
add hl,hl
add hl,hl
add hl,de
;hl=9

```
