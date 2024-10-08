


**************************************************


MariaDB インストール,Testrun


**************************************************


MariaDBのインストール/テストランです。

### Install

```
sudo pacman -S mariadb
```

### サービスの起動

mysqlというユーザーを作ります。

```
sudo systemctl enable mariadb.service
sudo mariadb-install-db --user=mysql --basedir=/usr --datadir=/val/lib/mysql
sudo systemctl start mariadb.service
```

### テストラン

テストランは一般ユーザーだと権限設定などがややこしいのでrootでします。

ログイン

```
sudo /usr/bin/mariadb -u root
```

サンプルデータベース作成

```
CREATE DATABASE sample_db;
USE sample_db;

CREATE TABLE sample_table (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    birthdata INT NOT NULL
);
```

データの挿入

```
INSERT INTO sample_table (name, birthdata) VALUES ('Maekawa Eriko', 19880517);
INSERT INTO sample_table (name, birthdata) VALUES ('Maekawa Taisuke',19670511);
```

データベースの表示

```
SELECT * FROM sample_table;
```

データの削除

```
DELETE FROM sample_table WHERE id=1;
```

