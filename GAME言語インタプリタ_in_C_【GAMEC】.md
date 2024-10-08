


**************************************************


GAME言語インタプリタ in C 【GAMEC】


**************************************************


GAME言語インタプリタ in C

昔懐かしのGAME言語のインタプリタをCで書いてみました。
基本的なシステムコールしか使ってないので、Cの動くシステムだったら全て動作すると思います。

GAME言語は今は廃れてしまいましたが、1980年前後にマイコンで手軽に使える処理系として一世を風靡しました。当時は、僕もGAME言語を参考に独自のインタプリタ・コンパイラを作って遊んでいたものです。

このプログラムはまだ未完成で、エラーチェックなどが十分ではないです。まあ、遊びだと思って下さい。一応制御構文などは全部動きます。

### コンパイル、インストール、実行
カレントディレクトリにgamec.cと、makefileを置き、makeとして下さい。
実行は、gamec [file.gm]として動かして下さい。
行末が0xa（LF)で終わるテキストを読み込みます。

makefile
```makefile
gamec: gamec.c
	cc -o gamec gamec.c
	sudo cp gamec /usr/bin/
```

### 履歴
・2023年1月31日 16ビット配列の取得の所にバグがあったので訂正。
・2023年2月1日　マイナーチェンジ＆名前をgamelinuxにする。
・2023年2月2日　バグ修正 ver 0.9
・2023年2月3日　getch()関数を内包 ver 0.9.1
・2023年2月4日　for文修正 ver 0.9.2
・2023年2月8日　for文の普通の言語モードで動作するのを　*FM 1に、GAME言語モードで動作するのを*FM 0にする。 ver 0.9.3
・2024年7月24日 数のサポートをushortからshortにする ver 1.0.0
・2024年8月24日 １文字入力をハードgetcharから、getcharにする。名前をgamecにする  ver 1.0.1
                
# 文法

・Jun Mizutani氏のGAME言語の文法を修正してアップします

```
<行番号>   1 〜 32767
<10進定数> -32768 〜 32767
<16進定数> $0000 〜 $FFFF
<文字定数> "文字"
<変数名>   A 〜 Z または冗長形(ABC等 先頭1文字が有効)

<2バイト配列> ::= 変数名 ( <式> )
                  変数の値 + 2 * 式の値 のアドレスの内容を値とする．

<1バイト配列> ::= 変数名 : <式> )
                  変数の値 + 式の値 のアドレスの内容を値とする．

<定数> ::= <10進定数> | <16進定数> | <文字定数>

<変数> ::= <変数名> | <1バイト配列> | <2バイト配列>

<式> ::= <項> | <項> <二項演算子> <項>

<項> ::= <定数> | <変数> | <配列> |（ <式> ）| <単項演算子> <項>　|  $  | ?
              $ は一文字入力、? は数値入力

<二項演算子> ::= + | - | * | / | = | <> | < | > | <= | >=
                比較演算 は 真:1, 偽:0の値を取る．

<単項演算子> ::= - | + | % | ' | #
                 + は絶対値, % は直前に実行した除算の余り,
                 ' は乱数, #は否定．
<行> ::= <行番号> スペース <文> [ 空白 <文> ] 改行
         | <行番号> スペース以外の文字 コメント 改行

<文>
    *AB              オプショナルコマンド　この場合はABというコマンド
    <変数>=<式>     変数への代入
    #=<式>            <式>の値の行番号の文にジャンプ(GOTO)
                      式の行番号がなければｓより大きい最初の行へジャンプ
    #=-1              プログラムの終了(END)
    !=<式>            <式>の値の行番号のサブルーチンへジャンプ(GOSUB)
    ]                 サブルーチンから戻る(RETURN)
    ;=<式>            式の値が真の場合は次の文に進み，
                      偽の場合は次の行を実行．
    @                 DO
    @=(式)            UNTIL
    変数=初期値,終値    FOR
    @=式              NEXT
    /                 改行出力
    "文字列"          文字列出力
    ?=<式>            <式>の結果を数値出力 左詰め
    ??=<式>           <式>の結果を数値出力 16進4桁
    ?$=<式>           <式>の結果の下位1バイトを数値出力 16進2桁
    ?(n)=<式>         <式>の値の数値出力 n桁で右詰め
    $=<式>            <式>の値の下位バイトを文字コードとする１文字を出力
    .=<式>            <式>の値の下位バイトの数だけ空白を出力
    '=<式>            <式>の値で乱数シードを設定

```

GAME言語についてはこのサイトが詳しいようです。

http://www43.tok2.com/home/cmpslv/Manual/GAME%20(J)(1979)(Ascii).pdf

GAME言語コンパイラ

https://qiita.com/fygar256/items/655bc44cc42f85c2f68f

# 本体

```plaintext:gamec.c
#include  <stdio.h>
#include  <ctype.h>
#include  <stdlib.h>
#include  <string.h>

short variable['Z'-'A'+1]= {0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0};
char *s;
u_char memory[65536];
char strbuff[256];
char pbuff[65536];
int psize=0;
int ln=0;
void *stack[65536];
int  sp=0;
int tron=0;
short mod=0;
int for_mode=0;

int skipspc()
{
    while(*s==' ')
        s++;
}

int load_source(char *s)
{
    FILE *ifp=fopen(s,"r");
    int idx=0;
    if (ifp) {
        while(idx<sizeof(pbuff)-1) {
            int c;
            c=fgetc(ifp);
            if (c==EOF) break;
            pbuff[idx]=c;
            idx++;
        }
        fclose(ifp);
    }
    psize=idx;
    pbuff[idx]='\0';
}

int load_src()
{
    char fn[256],*op=fn;
    while(*s==' ') s++;
    while(*s!='\n') *op++=*s++;
    *op='\0';
    load_source(fn);
}


int syntaxerror()
{
    printf("\nSyntaxerror in %d",ln);
    fflush(stdout);
}

int skipc(char c)
{
    if (*s==c) {
        s++;
        return(0);
    }
    syntaxerror();
}

char operator2()
{
    char c;
    switch (c=*s) {
    case '=':
    case '+':
    case '-':
    case'*':
    case '/':
        s++;
        return(c);
    case '<':
        s++;
        if (*s=='>') {
            s++;
            return('N');
        } else if (*s=='=') {
            s++;
            return('A');
        } else return('<');
    case '>':
        s++;
        if (*s=='=') {
            s++;
            return('B');
        } else return('>');
    default:
        return(0);
    }
}

char operator1()
{
    char c;
    switch (c=*s) {
    case '+':
    case '-':
    case '\'':
    case '#':
    case '%':
        s++;
        return(c);
    default:
        return(0);
    }
}


char var()
{
    char c=0;
    if (isalpha(*s)) {
        c=*s;
        while(isalpha(*s))
            ++s;
    }
    return(c);
}

short getval16()
{
    int i;
    if (!isxdigit(*s)) return(-1);
    sscanf(s,"%x",&i);
    while(isxdigit(*s)) ++s;
    return((short)i);
}

short getval10()
{
    short v;
    if (!isdigit(*s)) return(-1);
    v=atoi(s);
    while(isdigit(*s))
        ++s;
    return(v);
}

char *str()
{
    char *si=strbuff;
    if (*s=='"') {
        skipc('"');
        while(*s!='"')
            *si++=*s++;
        skipc('"');
    }
    *si='\0';
    return(strbuff);
}

short const_()
{
    short v;
    if (*s=='"') {
        char *si;
        si=str();
        v=si[0]+si[1]*256;
        return(v);
    } else if (*s=='$') {
        skipc('$');
        v=getval16();
        return(v);
    } else if (isdigit(*s)) {
        v=getval10();
        return(v);
    }
    return(0);
}

short expression();

short term()
{
    short v;
    char c;
    skipspc();
    if (*s=='(') {            // (exp)
        skipc('(');
        v=expression();
        skipspc();
        skipc(')');
        return(v);
    } else if (c=var()) {
        if (*s==':') {       // V:exp)
            skipc(':');
            v=expression();
            skipc(')');
            v=memory[variable[toupper(c)-'A']+v];
            return(v);
        } else if (*s=='(') { // V(exp)
            skipc('(');
            v=expression();
            skipc(')');
            v=(((u_char)memory[variable[toupper(c)-'A']+v*2+1])<<8)+((u_char)memory[variable[toupper(c)-'A']+v*2]);
            return(v);
        } else {             // V
            v=variable[toupper(c)-'A'];
            return(v);
        }
    } else if ((*s=='$') && (!isxdigit(*(s+1)))) { // getch
        s++;
        return(getchar());
    } else if (*s=='?') { // input
        int i;
        char buf[256];
        s++;
        scanf("%s",buf);
        if (buf[0]=='$')
            sscanf(buf+1,"%x",&i);
        else
            sscanf(buf,"%d",&i);
        return((short)i);
    } else if (v=const_()) // const
        return(v);
    else if (c=operator1(*s)) {  // term
        v=term();
        if (c=='-')
            return(-v);
        else if (c=='+')
            return(v<0?-v:v);
        else if (c=='#')
            return(!(v));
        else if (c=='\'') {
            int i;
            i=(short)(rand()%v);
            return(i);
        } else if (c=='%')
            return(mod);
    }
}

short expression()
{
    short v,v2;
    char c;
    skipspc();
    v=term();
    while(1) {
        if ((c=operator2())==0) break;
        v2=term();
        switch (c) {
        case '+':
            v+=v2;
            break;
        case '-':
            v-=v2;
            break;
        case '*':
            v*=v2;
            break;
        case '/':
            if (v2==0) {
                printf("Division by zero\n");
                v=-1;
                break;
            } else {
                mod=v%v2;
                v/=v2;
                break;
            }
        case '=':
            v=(v==v2);
            break;
        case '<':
            v=(v<v2);
            break;
        case 'N':
            v=(v!=v2);
            break;
        case 'A':
            v=(v<=v2);
            break;
        case '>':
            v=(v>v2);
            break;
        case 'B':
            v=(v>=v2);
            break;
        }
    }
    return(v);
}

int newline()
{
    while(1) {
        if (*s=='\n' || *s=='\0') break;
        s++;
    }
    if (*s=='\n') s++;
}

int searchline(short v)
{
    s=pbuff;
    if (s[0]=='#')
        while(*s++!='\n' );
    while(1) {
        if (s==pbuff+psize)
            return(-1);
        char *p=s;
        short n=getval10();
        s=p;
        if (n==-1)
            return(-1);
        if (n>=v)
            return(n);
        else
            newline();
    }
}

int go_to(short v)
{
    if (v==-1)
        ln=-1;
    else
        ln=searchline(v);
}

int go_sub(short v)
{
    stack[sp++]=s;
    go_to(v);
}

int return_()
{
    s=stack[--sp];
}

int do_()
{
    stack[sp++]=s;
}

int until_()
{
    char *p;
    short v;
    p=stack[--sp];
    v= expression();
    if (!v) {
        s=p;
        sp+=1;
    }
}

int next_()
{
    short *addr,to_,v;
    char *p;
    to_=(short )(ulong)stack[--sp];
    p=stack[--sp];
    addr=stack[--sp];
    *addr=v=expression();
    if (v<=to_) {
        s=p;
        sp+=3;
    }
}

int if_(short v)
{
    if (v==0) {
        newline();
        --s;
    }
}

int randseed(short v)
{
    srand(v);
}

int optional_command()
{
    char c1,c2;
    c1=toupper(*s++);
    c2=toupper(*s++);
    if (c1=='L' && c2=='D')
        load_src();
    else if (c1=='Q'&&c2=='U')
        exit(0);
    else if (c1=='T'&&c2=='N')
        tron=1;
    else if (c1=='T'&&c2=='F')
        tron=0;
    else if (c1=='S'&&c2=='H')
        system("bash");
    else if (c1=='F'&&c2=='M') {
        short v=expression();
        for_mode=v;
    } else syntaxerror();
}

int gameint()
{
    char c;
    short v;
    while(1) { /* line */
        c=*s;
        if (c=='\0')
            return(0);
        if (ln==-1)
            return(0);
        if (ln) {
            v=getval10();
            if (tron) {
                printf("[%d]",v);
                fflush(stdout);
            }
            ln=v;
            if (*s!=' ') {
                newline();
                continue;
            }
        }
        while(1) {
            c=*s;
            if (c=='\0') return(0);
            else if (c=='\n') {
                s++;
                break;
            } else if (c==' ') {
                skipspc();
                continue;
            } else if (c=='"') {
                printf("%s",str());
                fflush(stdout);
                continue;
            } else if (c=='/') {
                skipc('/');
                printf("\n");
                continue;
            } else if (c=='.') {
                skipc('.');
                skipc('=');
                v= expression();
                for(int i=0; i<v; i++)
                    printf(" ");
                fflush(stdout);
                continue;
            } else if (c=='*') {
                s++;
                optional_command();
                continue;
            } else if (c=='?') {
                s++;
                c=*s;
                if (c=='=') {
                    skipc('=');
                    v=expression();
                    printf("%d",(int)v);
                    fflush(stdout);
                    continue;
                } else if (c=='?') {
                    skipc('?');
                    skipc('=');
                    v=expression();
                    printf("%04hx",v&0xffff);
                    fflush(stdout);
                    continue;
                } else if (c=='$') {
                    skipc('$');
                    skipc('=');
                    v=expression();
                    printf("%02x",v&0xff);
                    fflush(stdout);
                    continue;
                } else if (c=='(') {
                    char buf[12],b2[128];
                    int v2;
                    skipc('(');
                    v2=expression();
                    skipc(')');
                    skipc('=');
                    v=expression();
                    snprintf(buf,12,"%d",v2);
                    b2[0]='\0';
                    strcat(b2,"%");
                    strcat(b2,buf);
                    strcat(b2,"d");
                    printf(b2,v);
                    fflush(stdout);
                    continue;
                }
                syntaxerror();
                return(0);
            } else if (c=='\'') {
                s++;
                skipc('=');
                v=expression();
                randseed(v);
                continue;
            } else if (c=='$') {
                s++;
                skipc('=');
                v=expression();
                printf("%c",v);
                fflush(stdout);
                continue;
            } else if (c=='#') {
                s++;
                skipc('=');
                v=expression();
                go_to(v);
                break;
            } else if (c=='!') {
                s++;
                skipc('=');
                v=expression();
                go_sub(v);
                break;
            } else if (c==']') {
                s++;
                return_();
                continue;
            } else if (c=='@') {
                s++;
                if (*s=='=') {
                    skipc('=');
                    if (*s=='(') { /* until */
                        until_(v);
                        continue;
                    } else { /* next */
                        next_();
                        continue;
                    }
                } else { /* do */
                    do_();
                    continue;
                }
            } else if (c==';') {
                s++;
                skipc('=');
                v=expression();
                if_(v);
                continue;
            } else if (c=var()) {
                short v,*addr;
                if (*s==':') {
                    short v2,vidx;
                    s++;
                    vidx=expression();
                    skipc(')');
                    skipc('=');
                    v2=expression();
                    memory[variable[toupper(c)-'A']+vidx]=v2&0xff;
                    continue;
                } else if (*s=='(') {
                    short vidx;
                    s++;
                    vidx=expression();
                    skipc(')');
                    skipc('=');
                    v=expression();
                    addr=(short *)&(memory[variable[toupper(c)-'A']+vidx*2]);
                    *addr=v;
                } else {
                    skipc('=');
                    v=expression();
                    addr=(short *)(&(variable[toupper(c)-'A']));
                    *addr=v;
                }
                if (*s==',') {
                    short to_;
                    skipc(',');
                    to_=expression();
                    if (v>to_ && (for_mode)) { // if for mode not equals to 0
                        //  and initial_value > to_value, skip to the end of next NEXT
                        int eof=0;
                        while(*s!='@') {
                            if (*s=='\0') {
                                eof=1;
                                break;
                            }
                            s++;
                        }
                        if (!eof) {
                            skipc('@');
                            skipc('=');
                            expression();
                        }
                    } else {
                        stack[sp++]=(char *)addr;
                        stack[sp++]=s;
                        stack[sp++]=(char *)(ulong)to_;
                    }
                }
                continue;
            }
            syntaxerror();
            return(0);
        }
    }
}

int main(int argc,char *argv[])
{
    psize=0;
    if (argc>=2) {
        load_source(argv[1]);
        go_to(1);
        gameint();
    } else {
        printf("Usage: gamelinux file\n");
    }
}
```

# サンプルプログラム

test1

```test.gm
#!/usr/bin/gamelinux
10"***comment ***"
100 "line 100 running "  !=10000
110 "line 110 running "  !=10000
120 "line 120 running "  !=10000
130 "line 130 running "  !=10000
9990 "end" /// #=-1
10000 "sub " 
10010 A=1,10 ?=A " " @=a+1 /
11000 ]

```

# サンプルプログラム実行結果

```
line 100 running sub 1 2 3 4 5 6 7 8 9 10 
line 110 running sub 1 2 3 4 5 6 7 8 9 10 
line 120 running sub 1 2 3 4 5 6 7 8 9 10 
line 130 running sub 1 2 3 4 5 6 7 8 9 10 
end
```
