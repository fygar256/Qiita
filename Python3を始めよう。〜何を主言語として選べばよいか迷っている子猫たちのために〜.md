


**************************************************


Python3を始めよう。〜何を主言語として選べばよいか迷っている子猫たちのために〜


**************************************************


Pythonは1991年に開発された言語で、組み込みから、webプログラミングから、アプリ開発から、自然言語処理から、人工知能から、ゲームも作れるし、GUIもあり、広汎に活用されている言語です。リスト処理はできるし、当然、ガベージコレクションもあるし、多倍長演算もできるし、オブジェクト指向としても、手続き型言語としても記述可能だし、初学者にもとっつきやすいし、基本的にクロスプラットフォームでLinux,MacOS,Windowsと使えるし、多彩なモジュール群があり、基本的にインタプリタで、実行速度は遅い部類ですが、コンパイラも用意されており、pythonは、今の、これからの言語と言えそうです。ただ、Python2は2020年に開発が終わるので、これから始めるのには、Python3が良いです。



ここでは、プラットフォームは、Ubuntu18.04LTSを取り上げます。

python3.6.xは、Ubuntu 18.04LTSに元々インストールされています。

python3.7の導入は、以下のページをお読みください。
<a href="https://www.sejuku.net/blog/83204"> Ubuntu18.04にPyhton3.7を導入や設定をしよう！</a>

コンパイラについては以下のページをお読みください。
<a href="https://ja.stackoverflow.com/questions/42597/python-%E3%81%AE%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%A0%E3%82%92%E5%AE%9F%E8%A1%8C%E5%8F%AF%E8%83%BD%E3%83%90%E3%82%A4%E3%83%8A%E3%83%AA%E3%81%AB%E3%82%B3%E3%83%B3%E3%83%91%E3%82%A4%E3%83%AB%E3%81%99%E3%82%8B%E3%81%AB%E3%81%AF"> Python のプログラムを実行可能バイナリにコンパイルするには？</a>

モジュールの導入は、pipを使ってやります。aptを使わなければインストールできないモジュールもあるようですが。

pipのインストールは、
`$ sudo apt-get install python3-pip`
と、します。

`$ python3 -m pip install SomePackage`
として、モジュールを導入します。

参考ページ：<a href="https://yutarine.blogspot.com/2018/07/ubuntu-python-apt-pip.html">UbuntuでPythonのモジュールをインストールしたいとき、aptとpipのどちらを使って入れようか？と思った話</a>

GUIの話。
<a href="https://hajipro.com/python/python-gui-best">【わかりやすく解説】PythonのGUIライブラリを比較10選 おすすめはどれ？</a>
<a href="https://qiita.com/SquidSky/items/90eb450310f1697d03e9"> Qiita:Pythonで簡単なGUIを作れる「Tkinter」を使おう</a>



この記事は一度、Qiitaにアップしましたが、誤って消してしまったのでもう一度上げます。
