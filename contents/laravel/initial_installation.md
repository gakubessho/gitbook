#### 前提
windows環境にvagrant,VirtualBoxをインストールしておく。

[参考](https://qiita.com/ozawan/items/160728f7c6b10c73b97e)

#### Homestead
LaravelではHomesteadというLaravelに必要なパッケージがインストールされたboxが提供されているため、
これを利用してvagrant上に開発環境を構築する。

#### Homesteadに含まれるソフトウェア
- Ubuntu*Git
- PHP 
- Nginx
- Apache (オプション)
- MySQL
- MariaDB (オプション)
- Sqlite3
- PostgreSQL
- Composer
- Node (Yarn、Bower、Bower、Grunt、Gulpを含む)
- Redis
- Memcached
- Beanstalkd
- Mailhog
- Elasticsearch (オプション)
- ngrok
※その他、bootstrap,vue.jsも標準でインストール済

今回は下記を利用  
OS→Ubuntu18.04.1  
webサーバ→Nginx1.15.0  
DB→MySQL5.7  
PHP→7.2  

#### セットアップ
```
#BOX追加
vagrant box add laravel/homestead
途中仮想マシンのproviderを確認されるので該当Noを入力

#Homsteadをダウンロードするディレクトリを作成
手順ではc:/dev/vagrant配下にlaravelディレクトリを作成
c:/dev/vagrant/laravel

#Homesteadクローン
※バックログにアップしたHomestead.zip利用の場合この手順は不要
cd c:/dev/vagrant/laravel
git clone https://github.com/laravel/homestead.git Homestead
bash init.sh
c:/dev/vagrant/laravel配下にHomesteadディレクトリが作成される

#Laravelプロジェクトクローン
※githubのlaravelプロジェクトをクローン
c:/dev/vagrant/laravel配下にcodeディレクトリを作成
cd code
Laravelプロジェクトをgitから取得

#sshキー作成
cd C:/Users/ユーザー名/
ssh-keygen -t rsa
C:/Users/ユーザー名/.ssh/にsshキーが作成される

#Homestead.yaml設定
※バックログにアップしたHomestead.zip利用の場合この手順は不要
cd  Homestead
vi  Homestead.yaml
folders:
    - map: c:/dev/vagrant/laravel/code #ローカル環境の共有ディレクトリを指定
      to: /home/vagrant/code/laravel  #vagrant環境の共有ディレクトリを指定

sites:
    - map: laravel #URLアクセスしたい名称
      to: /home/vagrant/code/laravel/public  #mapで指定した際に参照するディレクトリ

#hosts設定
Homestead.yamlに記載されているipをメモ
ip: "192.168.33.11"

C:\Windows\System32\drivers\etc\hostsにipとsites:mapを登録
192.168.33.11 laravel

#Vagrantfile設定
※バックログにアップしたHomestead.zip利用の場合この手順は不要
vi Vagrantfile
下記を追記
    config.vm.provision "shell", inline: <<-SHELL
	    mkdir -p /home/vagrant/code
	    mkdir -p /home/vagrant/code/laravel
	    mkdir -p /home/vagrant/code/node_modules 
	    sudo ln -sf /usr/share/zoneinfo/Japan /etc/localtime
	    sudo locale-gen ja_JP.UTF-8
	    sudo /usr/sbin/update-locale LANG=ja_JP.UTF-8
	    sudo timedatectl set-timezone Asia/Tokyo
    SHELL
    config.vm.provision :shell,
	    inline: "sudo mount --bind /home/vagrant/code/node_modules /home/vagrant/Code/laravel/node_modules",
	    run: "always"

#Homestead.yamlの設定反映
vagrant reload --provision

#vagrant立ち上げ
vagrant up

ブラウザから「http://laravel」にアクセスし画面が表示されれば成功

＃共有ディレクトリ確認
vagrant ssh
cd /home/vagrant/code/laravel
folders:mapで指定したディレクトリ内のファイルが確認できればOK

```