


**************************************************


arch linuxで、簡単にssh


**************************************************


arch linuxで、sshをインストールし、パスワードログインに設定します。

```
# インストール・起動時に有効化
sudo pacman -S openssh
sudo systemctl enable sshd

# パスワードログインのための設定
sudo vim /etc/ssh/sshd_config
PasswordAuthentication yes #コメントを外す

# sshdをスタートさせる
sudo systemctl start sshd

# テスト
ssh user@127.0.0.1
```
