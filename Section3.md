##Ansibleによる自動化とテスト

#Ansibleのインストール

    sudo apt-get install software-properties-common
    sudo apt-add-repository ppa:ansible/ansible
    sudo apt-get update
    sudo apt-get install ansible

#Vagrantfileに以下を追加

    if Vagrant.has_plugin?("vagrant-proxyconf")
        config.proxy.http = ENV['http_proxy']
        config.proxy.https = ENV['https_proxy']
        config.proxy.no_proxy = ENV['no_proxy']
    end

        config.vm.box = "CentOS7"
        config.vm.network "private_network", ip:"192.168.56.129"
        config.vm.provision "ansible" do |ansible|
        ansible.playbook = "playbook.yml"                                       end

#tempフォルダ、playbookの作成

 別ファイルに記述

#vagran init,vagrant up をしansibleによりサーバが立ち上がるか確認

