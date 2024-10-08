


**************************************************


Linux：systemdでの、DNSの置き換え。


**************************************************


systemdの走っているLinuxでは、DNSの置き換えは、
①
`/etc/systemd/resolved.conf`の、`#DNS=`と、コメントアウトされている所を

```
[Resolve]
DNS=8.8.8.8
```

のように、DNSサーバのIPアドレスを指定して、
②

```
systemctl restart systemd-resolved
```

で、適用。

③
```
systemd-resolve --status
```

で、確認します。

#不明な点

なお、デフォルトのDNSは、`systemd-resolve --status`で、先に出て来ませんでした。

systemdで提供されているリゾルバはローカルの127.0.0.53#53で、`nslookup`で名前検索しても、

```
$ nslookup facebook.com
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
Name:	facebook.com
Address: 31.13.82.36
Name:	facebook.com
Address: 2a03:2880:f10f:83:face:b00c:0:25de
```
としか出てきません。多分、①、②、③のステップを踏むと、systemd-resolved内部で書き換えたDNSサーバを呼び出すようになるのでしょう。
