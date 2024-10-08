


**************************************************


Debianで、suのパスワード入力を省略する


**************************************************


一々、ターミナルで、一般ユーザーからrootに移るとき、パスワードを入力しなくてはいけないのが面倒なので、パスワード入力を省略する方法を書いておきます。
スーパーユーザーで作業します。

(1)以下のコマンドを実行

```
 groupadd wheel
 adduser user wheel
 adduser root wheel
```
(2)/etc/pam.d/suを編集

```
# Uncomment the following line to implicitly trust users in the "wheel" group.
#auth            sufficient      pam_wheel.so trust use_uid

（こうなってるのを、下の一行をアンコメントする）

# Uncomment the following line to implicitly trust users in the "wheel" group.
auth            sufficient      pam_wheel.so trust use_uid |
```
 
