


**************************************************


お手軽uplatexテンプレートと、wxmaxima


**************************************************


uplatexを使い始めたはよいが、tex関係の情報がさんざん散らばっていてエラーが出まくっていたので、落ち着いたところで、覚書を兼ねて記しておきます。upLaTeXの迷える子猫だったので、まだ迷っている人のために。

tex latex platex uplatexは微妙に違うのですが、Ubuntuで、和文で書く事もあり、新しいuplatexを使うこととします。それぞれの違いは検索して調べて下さい。

# uplatexのインストール

```
sudo apt install texlive-lang-cjk xdvik-ja evince texlive-fonts-recommended texlive-fonts-extra
```

参考
[Qiita:【Ubuntu 18.04 LTS に LaTeX をインストール】](https://qiita.com/BitPositive/items/6b13e2038d628c33be8e)
[Qiita:【Ubuntu上でtexをコンパイルしてpdfとして保存する】]
(https://qiita.com/ocian/items/0e679e8bf72a39ada335)

# お手軽uplatex template
uplatexを、インストールしたのは良いけれど、さて、ヘッダやら何やらの書き方が分からない時、下記のテンプレートに書けば、大体通ります。

この、テンプレートは、`\parindent = 0pt`で、自動インデント抑止を、`\gt`でゴシック体を指定してます。`\parindent = 0pt`とするのは、バッドノウハウだと言われてますが。

```template.tex
\documentclass[uplatex]{jsarticle}
\usepackage{amsmath,amssymb}
\usepackage{txfonts}
\parindent = 0pt
\begin{document}
\gt

＜ここに内容を書く＞

\end{document}
```

これで、template.texを元に書いた、ファイルfoo.texから、
`$ uplatex foo.tex`で、.dviファイルを生成し、
`$ dvipdfm foo.dvi`で、.dviファイルから、.pdfに変換すると出来上がりです。
#コマンド入力を簡単にする。

下記のコードを、.bashrcに書いておくと良いでしょう。
`$ tex exsample`
とすると、exsample.texから、exsample.pdfを出力します。platexは、エラーで停止したら、どうにもこうにもコントロールがきかないので、エラーで止まるようにしています。
.bashrcを書き換えたら、
`$ source .bashrc`
とすると、反映されます。


```
#TeXLive
function tex {
  uplatex -halt-on-error "$1".tex
  dvipdfm "$1".dvi
}
```

# サンプルTeXコード

```R.tex
\documentclass[uplatex]{jsarticle}
\usepackage{amsmath,amssymb}
\usepackage{txfonts}
\parindent = 0pt
\begin{document}
\gt
\fontsize{15pt}{22pt}\selectfont

回転行列を求める。
\\
基本ベクトル
$\vec{e}=(1,0),\vec{f}=(0,1)$
を、角$\theta$回転すると、\\
$\vec{e'}=\left(\cos\theta,\sin\theta\right),\vec{f'}=\left(-\sin\theta,\cos\theta\right)$
になる。
\\


$\vec{p}=x\vec{e}+y\vec{f}$であった点は、
$\vec{p'}=x\vec{e'}+y\vec{f'}$に移る。\\
これを計算すると、\\
$\vec{p'}=x(\cos\theta,\sin\theta)+y(-\sin\theta,\cos\theta)\\
=(x\cos\theta-y\sin\theta,x\sin\theta+y\cos\theta)$.
\\

$\vec{p'}=(X,Y)$とすると、連立式\\

$
\left\{
\begin{array}{l}
X=x\cos\theta-y\sin\theta\\
Y=x\sin\theta+y\cos\theta
\end{array}
\right.
$
\\
を、行列の形に直して、
\\
\\
$
  \left(\begin{array}{rr}X \\ Y \end{array}\right)=
  \left(
    \begin{array}{rr}
      \cos\theta & -\sin\theta \\
      \sin\theta &  \cos\theta
    \end{array}
    \right)
  \left(\begin{array}{rr}x \\ y \end{array}\right)
$
\\
\\
よって、角$\theta$の回転行列$R$は、\\
$
R(\theta)=
  \left(
    \begin{array}{rr}
      \cos\theta & -\sin\theta \\
      \sin\theta &  \cos\theta
    \end{array}
    \right)
$
となる。

\end{document}

```

# 出力画像
<img width="800" alt="rotation" src="https://i.imgur.com/Tftgv1z.png">


# wxmaximaの出力をtexに貼り付ける時
tex関数というのがあって、`tex(式);`等とすると、maximaはtex風に数式を返して呉れますが、maximaの結果の出力表示は、TeX風ですがTeXではありません。それに、wxmaximaの出力を、右クリックして出てくるLATEX形式のテキストは、そのままでは、uplatexには使えませんでした。
予め、`load("mactex-utilities.lisp")$`としておいて、tex関数を使うと良いでしょう。

googleで、「mactex-utilities.lisp」として検索して下さい。

# online equation editor

オンラインの数式エディタは[【HostMath.com】](http://www.hostmath.com/)にあります。
