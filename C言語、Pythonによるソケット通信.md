


**************************************************


C言語、Pythonによるソケット通信


**************************************************


# C

C言語によるソケット通信のサンプルプログラムです。
ここでは、実験用に、1台のコンピュータでできるように、クライアントからループバックアドレス127.0.0.1に繋ぐようにしています。
あるウェブサイトを参考にしましたが、何処にあるか忘れましたので、言及出来ませんが、
初心者覚書です。

# ソースコード
sserver

```sserver.c
#include <stdio.h>
#include <string.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <unistd.h>
 
int main() {
    int sockfd;
    int client_sockfd;
    struct sockaddr_in addr;
 
    socklen_t len = sizeof( struct sockaddr_in );
    struct sockaddr_in from_addr;
 
    char buf[1024];
 
    // 受信バッファ初期化
    memset( buf, 0, sizeof( buf ) );
 
    // ソケット生成
    if( ( sockfd = socket( AF_INET, SOCK_STREAM, 0 ) ) < 0 ) {
        perror( "socket" );
    }
 
    // 待ち受け用IP・ポート番号設定
    addr.sin_family = AF_INET;
    addr.sin_port = htons( 1234 );
    addr.sin_addr.s_addr = INADDR_ANY;
 
    // バインド
    if( bind( sockfd, (struct sockaddr *)&addr, sizeof( addr ) ) < 0 ) {
        perror( "bind" );
    }
 
    printf("waiting for client's connection..\n");
    // 受信待ち
    if( listen( sockfd, SOMAXCONN ) < 0 ) {
        perror( "listen" );
    }
 
    // クライアントからのコネクト要求待ち
    if( ( client_sockfd = accept( sockfd, (struct sockaddr *)&from_addr, &len ) ) < 0 ) {
        perror( "accept" );
    }
 
    // 受信
    int rsize;
    while( 1 ) {
        rsize = recv( client_sockfd, buf, sizeof( buf ), 0 );
 
        if ( rsize == 0 ) {
            break;
        } else if ( rsize == -1 ) {
            perror( "recv" );
        } else {
            printf( "receive:%s\n", buf );
            sleep( 1 );
 
            // 応答
            printf( "send:%s\n", buf );
            write( client_sockfd, buf, rsize );
        }
    }
 
    // ソケットクローズ
    close( client_sockfd );
    close( sockfd );
 
    return 0;
}

```

sclient

```sclient.c
#include <stdio.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <unistd.h>
 
int main() {
    int sockfd;
    struct sockaddr_in addr;
 
    // ソケット生成
    if( (sockfd = socket( AF_INET, SOCK_STREAM, 0) ) < 0 ) {
        perror( "socket" );
    }
 
    // 送信先アドレス・ポート番号設定
    addr.sin_family = AF_INET;
    addr.sin_port = htons( 1234 );
    addr.sin_addr.s_addr = inet_addr( "127.0.0.1" );
 
    // サーバ接続
    connect( sockfd, (struct sockaddr *)&addr, sizeof( struct sockaddr_in ) );
 
    // データ送信
    char send_str[10];
    char receive_str[10];
    for ( int i = 0; i < 8; i++ ){
        sprintf( send_str, "%d", i );
        printf( "send:%d\n", i );
        if( send( sockfd, send_str, 10, 0 ) < 0 ) {
            perror( "send" );
        } else {
            recv( sockfd, receive_str, 10, 0 );
            printf( "receive:%s\n", receive_str );
        }
        sleep( 1 );
    }
 
    // ソケットクローズ
    close( sockfd );
 
    return 0;
}
```

# コンパイル

サーバは、`cc sserver.c -o ss`、クライアントは`cc sclient.c -o sc`でコンパイルしてください。

# 実験
まずsserverを立ち上げます。そして、もう一つの端末からsclientを立ち上げます。
そうすると、sclientから、8個のデータが送られてきてデータを表示し、終了です。

実験結果サーバ側

```
$ ./ss
waiting for client's connection..
receive:0
send:0
receive:1
send:1
receive:2
send:2
receive:3
send:3
receive:4
send:4
receive:5
send:5
receive:6
send:6
receive:7
send:7

```

実験結果クライアント側

```
$ ./sc
send:0
receive:0
send:1
receive:1
send:2
receive:2
send:3
receive:3
send:4
receive:4
send:5
receive:5
send:6
receive:6
send:7
receive:7
```

# Python3

Pythonでも書いてみました。此れはCのバージョンと同じ動きをします。
Cに比べて、ずいぶんとスッキリと書くことが出来ます。
参考<a href="https://qiita.com/nadechin/items/28fc8970d93dbf16e81b">Qiita:【Pythonによる通信処理】</a>
.encode()で、にバイナリにエンコードし、.decode()で、文字列にデコードします。utf-8をエンコードする時は、.encode('utf-8')でします。

ここでは、socket.gethostname()を使って、sserver.pyの自分のマシンのホストネームを得るようにしています。
sclient.pyでは、ループバックアドレス127.0.0.1に繋ぐようにしています。

```sserver.py
#!/usr/bin/python3
import socket

port = 1234

serversock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
serversock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
serversock.bind((socket.gethostname(),port)) #IPとPORTを指定してバインドします
serversock.listen(10) #接続の待ち受けをします（キューの最大数を指定）

print('Waiting for connections...')
clientsock, client_address = serversock.accept() #接続されればデータを格納

for i in range ( 8 ):
    rcvmsg = clientsock.recv(1024).decode()
    print('Received : %s' % (rcvmsg))
    s_msg = str(i)
    print('Send : %s' % s_msg)
    clientsock.sendall(s_msg.encode())
clientsock.close()

```

```sclient.py
#!/usr/bin/python3
import socket

host = "127.0.0.1"
port = 1234

client = socket.socket(socket.AF_INET, socket.SOCK_STREAM) #オブジェクトの作成を
します

client.connect((host, port)) #これでサーバーに接続します

for i in range(8):
  message = str(i)
  print('Send : %s' % message)
  client.send(message.encode()) #データを送信します
  response = client.recv(4096) #レシーブは適当な2の累乗にします（大きすぎるとダ>メ）
  print('Received: %s' % response.decode())

```
