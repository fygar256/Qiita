


**************************************************


Linux:grub rescueで'grub_file_filters' not foundが出る場合の復旧方法


**************************************************


Linuxシステムの、アップグレード時の不具合などで、`'grub_file_filters' not found`が出る場合の対処方法は、すぐ思いつくものとして、grubの再インストールがあります。とは言っても、システムが起動しないのならどうしようもないので、LiveDVDなどを用意して、そちらから起動します。
そして、端末を起動して、rootになって、以下のコマンドを叩いてやります。

```
mount /dev/sdx /mnt
mount --bind /sys /mnt/sys
mount --bind /proc /mnt/proc
mount --bind /dev /mnt/dev
chroot /mnt
grub-install /dev/sdx
```
ここで、sdxはブートドライブを指すデバイスです。
