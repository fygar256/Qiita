


**************************************************


LINUX:テンキーのないノートで、矢印キーを、2,4,6,8に対応させる方法


**************************************************


皆様おはようございます。
エミュレータで、PC-8801やPC-9801などのゲームをする時、方向キーのキーマップがテンキーの２，４，６，８にあてがわれていた場合、テンキーのないノートパソコンなどで、ゲームをする時困ります。そこで、今回は、xmodmapを使って、ノートパソコンの矢印キーをテンキーの２，４，６，８に対応させる方法をご紹介致します。


```xnp2m.sh

#!/bin/bash
(cd ~xnp2; xmodmap xmodemapm; xnp2; xmodmap xmodemapn ) &
```

ここでは、ディレクトリ~/xnp2にPC-9801エミュレータxnp2が入っているとします。

以下のキーマップをコピーして、~/xnp2にxmodemapm,xmodemapnとして入れてください。

```xmodemapm
!xmodemapm  (xmodmap modified)
keycode 111 = KP_8 KP_8 KP_8 KP_8
keycode 113 = KP_4 KP_4 KP_4 KP_4
keycode 114 = KP_6 KP_6 KP_6 KP_6
keycode 116 = KP_2 KP_2 KP_2 KP_2
```

```xmodemapn
!xmodemapn (xmodmap normal)
keycode 111 = Up NoSymbol Up
keycode 113 = Left NoSymbol Left
keycode 114 = Right NoSymbol Right
keycode 116 = Down NoSymbol Down
```

そして、xnp2m.shに実行権を付け実行するとエミュレータが起動します。
エミュレータが実行されている間は、マシンのキーボードマップが変わったままですが、エミュレータを終了すると、自動的にキーマップが元へ戻ります。

これで、某たいにゃん氏のmicroinvなどが動きます。
