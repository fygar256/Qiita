


**************************************************


Ubuntu 18.04 壊れてしまったswapfileの復元


**************************************************


# システムモニタなどで、スワップが「利用できません」と表示されたり、swapfileシステムが壊れてしまった時の復元方法

・以下のコマンドを実行する。（スーパーユーザーで作業します）

```
rm -f /swapfile
dd if=/dev/zero of=/swapfile bs=2M count=2048
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
vi /etc/fstab
```

fstabはスワップファイルが壊れてしまって再起動したら、スワップの行が消されてしまっているので、以下のように追加する。

```
/swapfile swap swap defaults 0 0
```
