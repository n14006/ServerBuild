##Section 6 AWS(Amazon Web Services)

#6-0 AWSコマンドラインインターフェイスのインストール

 以下のコマンドで、awsctlをインストール

    sudo apt-get install python-pip

    pip install awscli

#6-1 AWS EC2 + Ansible

 以下のコマンドを実行

    aws configure

accessKeyとSecretaccessKeyを入力

ec2でインスタンスを作成。以下のコマンドを実行して、名前をつける

    aws ec2 create-tags --resources i-e4dec417 --tags Key=Name,Value=n14006

ネットワークの設定を開いて、ゲートウェイを以下の値に編集

    172.16.40.10

以下のコマンドでsshを実行

    ssh -i キーペアファイル名.pem ec2-user@パブリックIP

playbookファイルを開いて、以下の部分を書き換えてAnsibleがec2サーバーで動くように設定する

    mariadb →  mysql
    mariadb-server →  mysql-server
    MySQL-python →  MySQL-python27

    mariadb →  mysqld

    /home/nginx/latest.tar.gz →  /home/latest.tar.gz

hostsファイルの作成

    [all]
    52.27.7.234

以下のコマンドを実行して、playbookを動かしてansibleでwordpressをインストールして、パブリックIPをURLにぶち込んで確認する

    ansible-playbook -i hosts -u ec2 playbook.yml --private-key /home/n14006/Downloads/n14006.pem

セキュリティグループにHTTPを追加

AMI(Amazon Machine Image)を作る

 前に作成したインスタンスのページを開き、右クリックでイメージを作成を選択。
 n14006_AMIという名前で作成し、保存。

 インスタンスを再度作成し、Amazon マシンイメージの選択で、マイAMIを選び、さっき作ったAMIを選択する。

 次の手順ボタンでステップ5：インスタンスのタグ付けまで飛ばす。

 値の入力欄に、好きな名前を入れる。

 次に進み、セキュリティグループの、ルールの追加でHTTPを選択して確認と作成を押す。

 作成したインスタンスのパブリックIPをコピーして、URLにぶち込んで、最初に作ったインスタンスのwordpressと同じページが表示されることを確認できればおk。

#6-2 AWS EC2(AMIMOTO)

 インスタンスを新たに作成する際にAWS Marketplace を選択し、検索欄にamimoto　と入力して検索する。

 PVM と HHVM が出てくるので、HHVM を選択。

 そのまま確認と作成を押して作成すると、エラーが出て作成できないので、セキュリティグループの編集から説明に書かれている日本語の説明文を消して、英語でわかりやすい説文に書き換える。

 すると無事に作成出来るので、パブリックIPをコピーして、下記のコマンドでターミナルからsshする

    ssh -i キーペア名.pem ec2-user@パブリックIP

 無事にsshでamimotoにログインが確認できたらおk。

 後はパブリックIPをURLにぶち込んでwordpressがインストールできたら終わり

#6-3 Route53

 Route53のページを開き、Hosted Zone を開いて、Create Hosted Zone でDomain Name と Commentにわかりやすい名前で記入して Create する。

 5-1で作ったzoneファイルの中身をcatコマンドで開き、コピーする。

 Import Zone File を押して、コピーした中身を貼り付けてImport する。

 更新して書き換えられているのを確認して終わり。

#6-4 S3

 S3のページを開いて、パケットを作成

 test用のhtmlファイルを作る

 以下のコマンドを入力

    aws s3 cp 作ったhtmlファイル s3://バケット名/

 S3のページに更新をかけ、test用のファイルが確認できればよい


