


**************************************************


NFSの簡単なセットアップ方法


**************************************************


スーパーユーザーで作業します。

```
aptitude -y install nfs-kernel-server
vi /etc/idmapd.conf
```

```idmapd.conf
# 6行目のコメントを外し、ドメイン設定をする。
Domain = localdomain
```

```
vi /etc/exports
```

```
# /etc/exportsの
# 最終行にマウント設定を書く
/home/nfs 192.168.1.0/24(rw,no_root_squash)
/mnt/sdb1 192.168.1.0/24(rw,no_root_squash)
```

```
systemctl restart nfs-kernel-server
```
