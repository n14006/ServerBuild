#Section1

##CentOS 7のインストール

###VirtualBoxへのインストール

*[CentOSの公式サイト](http://www.centos.org/download/)よりCentOS 7 Minimal ISO(x86_64)のISOファイルをダウンロードし、 VirtualBox上にインストール

*仮想マシンのメモリのサイズは1GB,ストレージ容量は8GB

*ネットワークアダプター２を設定。割り当てを「ホストオンリーアダプター」

* インストール中、root以外の作業用(管理者)のユーザを作成

###ネットワークアダプター1/2へのIPアドレスの設定とssh接続の確認

*/etc/sysconfig/network-scriptにifcfg-enp0s?のファイルを編集
　*ONBOOTをnoからyes

*以下のコマンドでIPアドレスを確認
　　　　
　　　　ip　a

###SSH接続の確認

*ubuntuからsshで仮想マシンに接続できることを確認

###インストール後の設定

*yumやwgetを使用するときのproxyの設定

　*/etc/yum.confの中に以下を追加

    proxy=http://172.16.40.1:8888

  *wgetをapt-getし、/etc/wgetrcの中に以下を追加

    https_proxy = http://172.16.40.1:8888/
    http_proxy = http://172.16.40.1:8888/
    ftp_proxy = http://172.16.40.1:8888/

###アップデート

*以下のコマンドでアップデートをかける

    yum update

##Wordpressを動かす(1)

*yumを使用して以下の三つをインストール

 *Apache HTTP Server
 *MySQL
 *PHP

###Apache HTTP Server

*以下のコマンドでインストール

    yum -y install httpd

*起動

    systemctl start httpd

###MySQL

*以下のコマンドでインストール

    yum -y install mysql mysql-devel mysql-server mysql-utilities

*起動

    systemctl start mysql

*mysqlへログイン

    mysql -u root -p

*データベースとユーザの作成

    CREATE database wordpress;

    grant all privileges on wordpress.* to 'n14006'@'localhost' idntified by 'pass';

    exit

###PHP

*以下のコマンドでインストール

     yum -y install http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm

     yum -y install php php-mysql php-gd php-mbstring

##Wordpressのインストール

*[公式サイト](http://ja.wordpress.org/)からダウンロードページのURLをコピー

*以下のコマンドでダウンロード・解凍

    wget https://ja.wordpress.org/wordpress-4.2.2-ja.zip

    unzip https://ja.wordpress.org/wordpress-4.2.2-ja.zip

*WordPress解凍先ディレクトリを/var/www/html/wpressディレクトリ下へ移動

    mv wordpress/ /var/www/html/wpress

*WordPressディレクトリwpress（「wpress」は例示）を一時的に書込可にする

    chmod 777 /var/www/html/wpress

*WordPressディレクトリとその中の全ファイルの所有者をtu、
グループをApache実行ユーザへ変更（「tu」は自分のユーザー名の例示。「apache:apache」も可）

     chown -R tu:apache /var/www/html/wpress/

*uploadsフォルダの作成

    mkdir /var/www/html/wpress/wp-content/uploads

*upgradeフォルダの作成

    mkdir /var/www/html/wpress/wp-content/upgrade

*p-contentフォルダとその中の全ファイルに読み書き権限の設定

    chmod -R 777 /var/www/html/wpress/wp-content

*ダウンロードしたファイルを削除

    rm -f https://ja.wordpress.org/wordpress-4.2.2-ja.zip

###SElinuxを無効化

*/etc/sysconfig/selinuxのSELINUX=disableへ変更

###Wordplessの初期設定

*ブラウザでhttp://サーバー名/wpress/へアクセスし、初期設定を行う。
