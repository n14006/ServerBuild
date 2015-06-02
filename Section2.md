# Section 2 その他のWebサーバー環境

## 2-1 Vagrantを使用したCentOS7環境の起動

###Vagrant用のディレクトリを作成してCentOS boxを用意してその中で作業をする
    mkdir CentOS
    vagrant box add BOX名 BOXのURL

####Vagrantfileの作成   
    vagrant init

####Vagrantfileの編集   
    Vagrant.configure(2) do |config|

    config.vm.box = "BOX名"

    config.vm.network "private_network", ip:"xxx.xxx.xxx.xxx"

## 2-2 Wordpressを動かす(2)

### PHPのインストールとバージョンの確認

    yum -y install php-mysql php php-gd php-mbstring php-fpm

    php --version

### '/etc/php-fpm.d/www.conf'を編集。以下を追加または、変更。

    listen = 127.0.0.9000

    listen.allowed_clients = 127.0.0.1

    user = nginx

    group = nginx

    pm = static

### デーモンの起動

    systemctl start php-fpm.service

    systemctl enable php-fpm.service

### mariadb(mysql)のインストール

    yum -y install mariadb mariadb-server

    systemctl start mariadb.service

    systemctl enable mariadb.service

### [公式サイト](http://nginx.org/en/linux_packages.html#stable)からリポジトリ追加用のrpmをダウンロードしてインストール

    wget rpmパッケージURL

    rpm -ivh rpmパッケージ名

### nginxのインストール

    yum -y install nginx

### Wordpressで使用するデータベースの作成

#### ホームディレクトリに'wordpress.sql'を作成する

    set password for root@localhost=password('roothoge');

    insert into user set user="ユーザー名", password=password("パスワード"), host="localhost";

    create database データベース名;

    grant all on データベース名.* to hoge;

    FLUSH PRIVILEGES;

#### データベースの確認

    mysql -uroot -Dmysql < wordpress.sql

    mysql -uroot -pパスワード

MariaDBにログイン出来ることを確認したら

    exit

#### 新規データベースへ新規ユーザーでログイン出来るかの確認

    mysql -uユーザー名 -pパスワード -Dwddb
    
確認できる

    exit

### 念のための処置

    systemctl stop httpd.service

    systemctl disable httpd.service

    sudo systemctl restart madiadb.service nginx php-fpm

    systemctl enable nginx.service

### ログ出力ディレクトリの作成

    mkdir -p /var/log/nginx/www.example.com

    chown nginx: /var/log/nginx/www.example.com

    chmod +r+w /var/log/nginx/www.example.com

### /etc/nginx/conf.d/default.confの編集

    rootディレクトリにしたい場所のパス(URL)を記述する
    phpのコメントアウトを外してrootディレクトリのパスを記述する
    $document_root$fastcgi_script_name;に変更する


### Wordpressのインストール

    wget http://wordpress.org/latest.tar.gz

    tar -xzvf latest.tar.gz

    mv wordpress default.confで設定したパス

ブラウザでの表示の確認をする

## 2-3 Wordpressを動かす(3)

### apache2.2のインストール

    yum -y install wget gcc openssl openssl-devel

    wget http://ftp.kddilabs.jp/infosystems/apache//httpd/httpd-2.2.29.tar.gz
    tar zxvf ./httpd-2.2.29.tar.gz

    cd ./httpd-2.2.29

    ./configure --enable-suexec

    make

    make install

### Apacheの起動

    /usr/local/apache2/bin/apachectl start

### PHPソースファイルからのインストール

    sudo yum install libxml2 libxml2-devel

    sudo wget --trust-server-names http://jp2.php.net/get/php-5.5.25.tar.gz/from/this/mirror

    tar zxvf php-5.5.25.tar.gz

    cd php-5.5.25

    ./configure --enable-so

    make

    make install

    ./configure --with-apxs2=/usr/local/apache2/bin/apxs --with-mysql

    make

    make install

### php.iniの作成

    cp php.ini-development /usr/local/lib/php.ini

### '/usr/local/lib/php.ini'の編集

   mysql.default_socket = /var/lib/mysql/mysql.sock


### '/usr/local/apache2/conf/httpd.conf'の編集

    ServerName ユーザー名

    <FilesMatch \.php$>
      SetHandler application/x-httpd-php
    </FilesMatch>

### index.phpを追加する   
    <IfModule dir_module>   
        DirectoryIndex index.html index.php   
    </IfModule>


### データベースの作成

    sudo yum install mriadb mariadb-server

    sudo systemctl start mariadb.service

    mysql -uroot -p

    create database データベース名;

    grant all privileges on データベース名.* to ユーザー名@localhost identif    ied by 'パスワード';

    exit

### デーモンの再起動

    sudo  /usr/local/apache2/bin/apachectl restart

    sudo systemctl start mariadb.service


## 2-4 ベンチマークを取る

### abコマンドのインストール

    sudo apt-get install apache2-utils

### PageSpeed

    Google Chrome > 設定 > 拡張機能 > 他の拡張機能 
    から「Page Speed Insight(with PNaCI)」を追加する 

### プラグインのインストール

    cd php-5.5.25

    ./configure --with-apxs2=/usr/local/apache2/bin/apxs --enable-mbstring --enable-mbregex --with-mysql --with-mysqli --with-pdo-mysql --with-pear --with-zlib --prefix=/usr/local/php5.5 --enable-module=so --with-openssl 
     
    make

    sudo make install

    cp php.ini-development /usr/local/lib/php.ini

    cd

    wget https://downloads.wordpress.org/plugin/wp-super-cache.1.4.4.zip

    unzip wp-super-cache.1.4.4.zip
     
    mv wp-super-cache.1.4.4 /usr/local/apache2/htdocs/wordpress/wp-content/plugins/

### '/usr/local/lib/php.ini'の編集

    mysql.default_socket = /var/lib/mysql/mysql.sock
     
Wordpressでプラグインを有効にして、ベンチマークを実行

###PageSpeed

#### プラグイン導入前

    Connection Times (ms)
    min  mean[+/-sd] median   max
    Connect:        1    2   0.6      2       3
    Processing:  2197 2250  29.9   2275    2278
    Waiting:     1975 2142  94.4   2158    2259
    Total:       2198 2252  29.8   2277    2279

    
#### プラグイン導入後

    Connection Times (ms)   
    min  mean[+/-sd] median   max   
    Connect:        1    1   0.2      1       1
    Processing:  2401 2583  75.5   2619    2635
    Waiting:     2311 2457  86.5   2468    2613
    Total:       2402 2584  75.6   2620    2636
               

## 2-5 セキュリティチェック

