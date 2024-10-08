


**************************************************


expression.py python用式の評価モジュール


**************************************************


pythonで書いた式の評価モジュール
16進整数、10進整数、floatを数として使える。

演算子と優先順位はpythonを基にして次の通り
(expression)            括弧で囲った式
-,~                     負、ビットNOT
*,/                     乗算、整数除算
+,-                     加算、減算
<<,>>                   左シフト、右シフト
&                       ビットAND
|                       ビットOR
'                       符号拡張
<=,<,>,>=,!=,==         比較演算子
!x                      論理NOT
&&                      論理AND
||                      論理OR

'演算子について
a'24とすると、aの24ビット目を符号として符号拡張をします。オリジナルの演算子です。

### 使い方

`import expression`として、インポートし、
`(value,index)=expression.expression(string,0)`のように呼び出してください。
stringに式を与え、0は文字列のどこから評価を始めるかのインデックスです。
stringの後ろには番人としてchr(0)を付けておいてください。番人は処理が簡単なため付けました。

valueに式の値が、indexに次のインデックスが返ります。

わざわざ、自分で式評価プログラムを書かなくても、pythonではevalがあります。

```expression.py
#!/usr/bin/python3
import  sys

def err(m):
    print(m)
    return -1

def factor(s,idx):
    if s[idx]=='-':
        (x,idx)=factor(s,idx+1)
        x=-x
    elif s[idx]=='~':
        (x,idx)=factor(s,idx+1)
        x=~x
    else:
        (x,idx)=factor1(s,idx)
    return (x,idx)

def factor1(s,idx):
    x = 0

    if s[idx]=='0' and (s[idx+1]=='x' or s[idx+1]=='X'):
        idx+=2
        while(s[idx].upper() in "0123456789ABCDEF"):
            x=16*x+(ord(s[idx])-0x30 if s[idx] in "0123456789" else ord(s[idx].upper())-0x41+10 )
            idx+=1

    elif s[idx] in "0123456789":
        while(s[idx] in "0123456789"):
            x=10*x+ord(s[idx])-0x30
            idx+=1
        a=1
        if (s[idx]=='.'):
            idx+=1
            while(s[idx] in "0123456789"):
                x+=(a/10)*(ord(s[idx])-0x30)
                a/=10
                idx+=1

    elif s[idx]=='(':
        (x,idx)=expression(s,idx+1)
        if s[idx]!=')':
            err("Missing ')'.")
            idx=-1
        else:
            idx+=1
    return (x,idx)

def term0(s,idx):
    (x,idx)=factor(s,idx)
    while True:
        if (s[idx]=='*'):
            (t,idx)=factor(s,idx+1)
            x*=t
        elif (s[idx]=='/'):
            (t,idx)=factor(s,idx+1)
            if t==0:
                err("Division by 0 error.")
                idx=-1
                x=-1
            else:
                x//=t
        elif s[idx]=='%':
            (t,idx)=factor(s,idx+1)
            x=x%t
        else:
            break
    return (x,idx)

def term1(s,idx):
    (x,idx)=term0(s,idx)
    while True:
        if (s[idx]=='+'):
            (t,idx)=term0(s,idx+1)
            x+=t
        elif (s[idx]=='-'):
            (t,idx)=term0(s,idx+1)
            x-=t
        else:
            break
    return (x,idx)

def term2(s,idx):
    (x,idx)=term1(s,idx)
    while True:
        if (s[idx]=='<' and s[idx+1]=='<'):
            (t,idx)=term1(s,idx+2)
            x<<=t
        elif (s[idx]=='>' and s[idx+1]=='>'):
            (t,idx)=term1(s,idx+2)
            x>>=t
        else:
            break
    return (x,idx)

def term3(s,idx):
    (x,idx)=term2(s,idx)
    while True:
        if (s[idx]=='&' and s[idx+1]!='&'):
            (t,idx)=term2(s,idx+1)
            x=int(x)&int(t)
        else:
            break
    return (x,idx)


def term4(s,idx):
    (x,idx)=term3(s,idx)
    while True:
        if (s[idx]=='|' and s[idx+1]!='|'):
            (t,idx)=term3(s,idx+1)
            x=int(x)|int(t)
        else:
            break
    return (x,idx)

def term5(s,idx):
    (x,idx)=term4(s,idx)
    while True:
        if (s[idx]=='^'):
            (t,idx)=term4(s,idx+1)
            x=int(x)^int(t)
        else:
            break
    return (x,idx)

def term6(s,idx):
    (x,idx)=term5(s,idx)
    while True:
        if (s[idx]=='\''):
            (t,idx)=term5(s,idx+1)
            x=(x&~((~0)<<t))|((~0)<<t if (x>>(t-1)&1) else 0)
        else:
            break
    return (x,idx)

def term7(s,idx):
    (x,idx)=term6(s,idx)
    while True:
        if (s[idx]=='<' and s[idx+1]=='='):
            (t,idx)=term6(s,idx+2)
            x=x<=t
        elif (s[idx]=='<'):
            (t,idx)=term6(s,idx+1)
            x=x<t
        elif (s[idx]=='>' and s[idx+1]=='='):
            (t,idx)=term6(s,idx+2)
            x=x>=t
        elif (s[idx]=='>'):
            (t,idx)=term6(s,idx+1)
            x=x>t
        elif (s[idx]=='=' and s[idx+1]=='='):
            (t,idx)=term6(s,idx+2)
            x=x==t
        elif (s[idx]=='!' and s[idx+1]=='='):
            (t,idx)=term6(s,idx+2)
            x=x!=t
        else:
            break
    return (x,idx)

def term8(s,idx):
    if s[idx]=='!':
        (x,idx)=term8(s,idx+1)
        x=not x
    else:
        (x,idx)=term7(s,idx)
    return (x,idx)

def term9(s,idx):
    (x,idx)=term8(s,idx)
    while True:
        if (s[idx]=='&' and s[idx+1]=='&'):
            (t,idx)=term8(s,idx+2)
            x=x and t
        else:
            break
    return (x,idx)

def term10(s,idx):
    (x,idx)=term9(s,idx)
    while True:
        if (s[idx]=='|' and s[idx+1]=='|'):
            (t,idx)=term9(s,idx+2)
            x=x or t
        else:
            break
    return (x,idx)


def expression(s,idx):
    s+=chr(0)
    return term10(s,idx)

def main():
    s=input("Input expression:")
    x,idx=expression(s,0)
    print(x)

if __name__=='__main__':
    main()
    exit(0)

```
