


**************************************************


snapのcoreを含む全ての機能のアンインストール


**************************************************


スーパーユーザで作業します。

```
apt-get purge snap snapd
umount $(mount | grep snap | awk '{print $3}')
rm -rf /var/lib/snapd
rm -rf /var/snap
find /etc/systemd/system -name "*snap-*.mount" -delete
find /etc/systemd/system -name "snap.*.service" -delete
rm -rf /snap
```

以上。
