


**************************************************


FreeBSDのGRUBエントリ


**************************************************



主OSをFreeBSDに乗り換えようと思ってインストールしてみましたが、やはり、動くアプリが少なそうなので、主OSを乗り換えるのは止めました。マルチブートにします。そのときのGRUBのcustom.cfgを記しておきます。

```custom.cfg
menuentry "FreeBSD 13.1" {
        insmod ufs2
        set root=(hd0,2)
        kfreebsd /boot/loader
        search --no-floppy --fs-uuid 6411d3a0b7258e16
}
```

(hd0,2)は、ディスク1(0から数えます)の2番めのパーティションにFreeBSDがあるということです。
UUIDは適宜変更。
