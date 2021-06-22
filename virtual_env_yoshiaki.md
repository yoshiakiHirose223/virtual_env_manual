# 環境仕様書

## インストールするもの
- VirtualBox
- Vagrant
- PHP
- Nginx
- Laravel

## 仮想環境構築の流れ
1. VirtualBoxをPCにインストールします。<br>VirtualBoxは使用しているPCにインストールされているOSとは別の仮想的なOSを立ち上げるためのソフトウェアです。

2. VagrantをPCにインストールします。<br>VirtualBoxのみでも仮想環境自体を構築することはできますが、Vagrantを使用することでより効率良く構築することが可能になります。
例えば、構築したい仮想環境の内容をファイルで管理し、コマンド一つで起動や破壊を行うことができます。

3. Vagrantを使って仮想環境のOSをダウンロードします。

4. 作業用ディレクトリを作成し、設定ファイルの生成、仮想環境に使うOSを指定します。

5. Vagrantfileを編集します。

## やってみよう
### VirtualBoxのインストール
___
VirtualBoxを[公式サイト](https://www.virtualbox.org/wiki/Download_Old_Builds_6_0)からダウンロード後、インストールしてください。<br>
* MacOSの方→**OS X hosts** を選択<br>※macOS Big Surを使用している方は最新版のVirtualBox 6.0.24をインストールしてください
* WindowsOSの方→**Windows hosts** を選択<br>


#### Mac
コマンドを実行し、VirtualBoxのウィンドウが表示されれば正常にインストールされています。
```
$ virtualbox
```
#### Windows
デスクトップに Oracle VM VirtualBox のアイコンがあるか確認してください。あればインストールは完了しています。

### Vagrantのインストール
___
#### mac
Homebrewを使ってインストールします。<br>
下記コマンドを実行してください。
```
$ brew cask install vagrant
```
Homebrewのバージョンが新しいと、caskコマンドがオプションになっており、上記のコマンドでエラーになる場合があります。その時は下記のコマンドでインストールしてください。
```
$ brew install --cask vagrant
```
インストールが終わったら vagrant コマンドが使用可能かどうか確認するため以下のコマンドを実行しましょう。バージョンが確認できたらインストールできています。
```
$ vagrant -v
```
#### Windows
Vagrantの[公式サイト](https://www.vagrantup.com/)からインストールしてください。
インストールが終わったら vagrant コマンドが使用可能かどうか確認するため以下のコマンドを実行しましょう。バージョンが確認できたらインストールできています。
```
$ vagrant -v
```
___
### 仮想環境のOSをダウンロード
___
Vagrantを使ってOSをダウンロードします。
今回はLinuxのCentOSのバージョン7のbox名 centos/7 を指定して実行してみましょう。
```
vagrant box add centos/7
```
コマンドを実行すると、下記のような選択肢が表示されます。
```
1) hyperv
2) libvirt
3) virtualbox
4) vmware_desktop

Enter your choice:
```
今回使用するソフトはVirtualBoxのため、3を選択してenterを押しましょう。
```
Enter your choice: 3
```
下記のように表示されたら完了です。

```
Successfully added box 'centos/7' (v1902.01) for 'virtualbox'!
```
___
Vagrantの作業用ディレクトリを作成
___
Vagrantの作業用ディレクトリを作成します。<br>
作成したディレクトリを移動させると、ゲストOSへのログインが上手くいかなくなってしまうので、自分が作業しやすいディレクトリに作成するのが良いでしょう。<br>
<例>
 - デスクトップ
 - 自分の作業用ディレクトリ<br>

```
mkdir vagrant_test
```
作成したディレクトリに移動しましょう。
```
cd vagrant_test
```
Vagrantの設定ファイルを生成すると同時にOSをBox名で指定します。
```
vagrant init centos/7
```
実行後以下のような文言が表示されたら成功です。
```
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```
___
### Vagrantfileの編集
___
Vagrantfileはこれから稼働させる仮想環境の設計書のようなものです。これをもとに仮想環境が構築されます。<br>
変更する点は3つあります。
まずは変更点①、変更点②に該当する行の**\#**を削除してください。
```
# 変更点①
config.vm.network "forwarded_port", guest: 80, host: 8080

# 変更点②
config.vm.network "private_network", ip: "192.168.33.10"
```
次に該当の箇所を以下のように編集してください
```
# 変更点③
config.vm.synced_folder "../data", "/vagrant_data"
# ↓ 以下に編集
config.vm.synced_folder "./", "/vagrant", type:"virtualbox"
```
変更後、上書き保存をして終了してください。
___
Vagrant プラグインのインストール
___
vagrant-vbguest というプラグインをインストールします。<br>
agrant-vbguestは初めに追加したBoxの中にインストールされているGuest Additionsというもののバージョンを、VirtualBoxのバージョンに合わせて最新化してくれるプラグインです。<br>
下記のコマンドを実行し、インストールしてください。
```
vagrant plugin install vagrant-vbguest
```
下記のコマンドを実行しvagrant-vbguestのインストールが完了しているか確認してください。
```
vagrant plugin list
```
___
### ゲストOSの起動
___
Vagrantfileがあるディレクトリにて以下のコマンドを実行して、起動してみましょう。
```
vagrant up
```
___
### ゲストOSへのログイン
___
#### mac
vagrant_test ディレクトリに移動して下記のコマンドを実行しましょう。
```
vagrant ssh
```
コマンドを実行した後、以下のような表記になっていればゲストOSにログインしていることになります。
```
Welcome to your Vagrant-built virtual machine.
[vagrant@localhost ~]$
```
___
必要なパッケージのインストール
___
現段階でゲストOSは立ち上がりましたが、開発するにあたって必要となるソフトウェアやコマンドなどがインストールされていない状態です。<br>
まずはグループパッケージをインストールしていきましょう。
下記のコマンドを実行してください。gitなどの開発に必要なパッケージを一括でインストールできます。
```
sudo yum -y groupinstall "development tools"
```
___
PHPのインストール
___
yumコマンドを使用してPHPをインストールした場合、古いバージョンのPHPがインストールされてしまいます。
Laravelを動作させるにはPHPのバージョン7以上をインストールする必要があるため
yumではなく外部パッケージツールをダウンロードして、そこからPHPをインストールしていきます。
まずは、PHPのインストールに必要な外部パッケージツールをダウンロードします。
```
sudo yum -y install epel-release wget
sudo wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
sudo rpm -Uvh remi-release-7.rpm
```
次にPHPをインストールし確認のためバージョンをみるコマンドを実行します。
```
sudo yum -y install --enablerepo=remi-php72 php php-pdo php-mysqlnd php-mbstring php-xml php-fpm php-common php-devel php-mysql unzip
php -v
```
バージョンが確認できればインストールの完了です。
___
composerのインストール
___
PHPのパッケージ管理ツールであるcomposerをインストールしていきます。
```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"

# どのディレクトリにいてもcomposerコマンドを使用できるようfileの移動を行います
sudo mv composer.phar /usr/local/bin/composer
composer -v
```
composer のバージョンが確認できたら、composerのインストールは完了です。
___
Laravelアプリケーションのコピー
___
LaravelアプリケーションをゲストOS内で動かすためにLaravelプロジェクトのコピーをvagrant_testディレクトリ下に作成します。
```
cd vagrant_test
cp -r laravel_appディレクトリまでの絶対パス ./
```
___
### ゲストOS内にWebサーバーとなるNginxをインストール
___
Nginxの最新版をインストールしていきます。
viエディタを使用して以下のファイルを作成します。
```
sudo vi /etc/yum.repos.d/nginx.repo
```
以下のように編集してください
```
[nginx]
name=nginx repo
baseurl=https://nginx.org/packages/mainline/centos/\$releasever/\$basearch/
gpgcheck=0
enabled=1
```
書き終えたら保存して、以下のコマンドを実行しNginxのインストールを実行します。
```
$ sudo yum install -y nginx
$ nginx -v
```
Nginxのバージョンが確認できたらインストール完了です
Nginxの起動をしましょう。
```
sudo systemctl start nginx
```
ブラウザにて [http://192.168.33.10](http://192.168.33.10) (Vagrantfileでipを書き換えた方はそのipアドレス)と入力し、NginxのWelcomeページが表示されましたでしょうか？
表示されたら問題なく動いています。
___
### Laravelを動かす
___
Nginxにも設定ファイルが存在しているので編集を行います。
使用しているOSがCentOSの場合、`/etc/nginx/conf.d` ディレクトリ下の ``default.conf` ファイルが設定ファイルとなります。
```
sudo vi /etc/nginx/conf.d/default.conf
```
以下に従って編集してください
```
server {
  listen       80;
  server_name  192.168.33.10; # Vagranfileでコメントを外した箇所のipアドレスを記述してください。
  # ApacheのDocumentRootにあたります
  root /vagrant/laravel_app/public; # 追記
  index  index.html index.htm index.php; # 追記

  #charset koi8-r;
  #access_log  /var/log/nginx/host.access.log  main;

  location / {
      #root   /usr/share/nginx/html; # コメントアウト
      #index  index.html index.htm;  # コメントアウト
      try_files $uri $uri/ /index.php$is_args$args;  # 追記
  }

  # 省略

  # 該当箇所のコメントを解除し、必要な箇所には変更を加える
  # 下記は root を除いたlocation { } までのコメントが解除されていることを確認してください。

  location ~ \.php$ {
  #    root           html;
      fastcgi_pass   127.0.0.1:9000;
      fastcgi_index  index.php;
      fastcgi_param  SCRIPT_FILENAME  /$document_root/$fastcgi_script_name;  # $fastcgi_script_name以前を /$document_root/に変更
      include        fastcgi_params;
  }
```
Nginxの設定ファイルの変更は、以上です。
次に php-fpm の設定ファイルを編集していきます。
```
sudo vi /etc/php-fpm.d/www.conf
```
以下に従って編集してください
```
;24行目近辺
user = apache
# ↓ 以下に編集
user = nginx

group = apache
# ↓ 以下に編集
group = nginx
```
設定ファイルの変更に関しては、以上となります。
では早速起動しましょう(Nginxは再起動になります)。
```
$ sudo systemctl restart nginx
$ sudo systemctl start php-fpm
```
再度ブラウザにて、 [http://192.168.33.10](http://192.168.33.10)にアクセスしてください
画面は表示されますが、以下のようなLaravelのエラーが表示されると思います。
```
The stream or file "/vagrant/laravel_app/storage/logs/laravel.log" could not be opened: failed to open stream: Permission denied
```
これは 先程php-fpmの設定ファイルの user と group を `nginx` に変更したと思いますが、ファイルとディレクトリの実行 user と group に `nginx` が許可されていないため起きているエラーです。
以下のコマンドを実行してみてください。
```
$ ls -la ./ | grep storage && ls -la storage/ | grep logs && ls -la storage/logs/ | grep laravel.log
```
出力結果から、storageディレクトリも logsディレクトリも laravel.logファイルも全て user と group が
vagrant となっていますので、これでは nginx というユーザーの権限をもってlaravel.logファイルへの書き込みができません。

では、以下のコマンドを実行して nginx というユーザーでもログファイルへの書き込みができる権限を付与しましょう。
```
$ cd /vagrant/laravel_app
$ sudo chmod -R 777 storage
```
権限を変更するコマンドを実行しましたが、nginxというユーザーが /laravel_app/storage/logs/laravel.log に対しての書き込み権限を得たのかどうかの確認がまだできていません。

laravel.logにはLaravelアプリケーション実行中にエラーが生じた場合に画面に表示されるエラー内容(皆さんも良く見たであろうWhoops)と全く同じ内容が書き込みされています。

そのため、意図的にアプリケーション実行エラーを起こしてlaravel.logに画面と同じエラーが表示されているか見てみましょう。
```
$ cd /vagrant/laravel_app
$ vi routes/web.php
```
以下のように編集してみましょう。
```
Route::get('/', function () {
    return view('welcome'); <-この;を消す。
});

↓ # ; を消します

Route::get('/', function () {
    return view('welcome')
});
```
編集が完了しましたら次は以下のコマンドを実行します。
```
$ tail -f storage/logs/laravel.log
```
再度 [http://192.168.33.10](http://192.168.33.10) のURLにアクセスしてみます。
```
syntax error, unexpected '}', expecting ';'
```
といった内容のエラーが画面上に表示され、同じ内容のエラー文言が`laravel.log`に書き込みされたことも確認できたでしょうか？？

確認が完了しましたら、`Ctrl + c `でtailの実行モードを終了しましょう。

では変更した `routes/web.php`の内容を元に戻して再度 [http://192.168.33.10](http://192.168.33.10) にアクセスして正常にLaravelのWelcome画面の表示をしてください。

___
### データベースのインストール
___
今回インストールするデータベースはMySQLとなります。versionは5.7を使用します。
rpmに新たにリポジトリを追加し、インストールを行います。
```
sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
sudo rpm -Uvh mysql57-community-release-el7-7.noarch.rpm
sudo yum install -y mysql-community-server
mysql --version
```
バージョンの確認ができたらインストール完了です。
次にMySQLを起動し接続を行います。
```
sudo systemctl start mysqld
mysql -u root -p
Enter password:
```
今回はデフォルトでrootにパスワードが設定されてしまっています。
まずはpasswordを調べ、接続しpassswordの再設定を行っていく必要があります。
```
sudo cat /var/log/mysqld.log | grep 'temporary password'  # このコマンドを実行したら下記のように表示されたらOKです
2017-01-01T00:00:00.000000Z 1 [Note] A temporary password is generated for root@localhost: hogehoge
```
hogehoge と記載されている箇所に存在するランダムな文字列がパスワードとなります。
```
$ mysql -u root -p
$ Enter password: hogehoge
mysql >
```
次に接続した状態でpasswordの変更を行います。
```
mysql > set password = "新たなpassword";
```
新たなpasswordには、必ず大文字小文字の英数字 + 記号かつ8文字以上の設定をする必要があります。
MySQL5.7のパスワードポリシーは厳格で開発段階では非常に面倒のため、以下の設定を行いシンプルなパスワードに初期設定できるようにMySQLの設定ファイルを変更します。
```
sudo vi /etc/my.cnf
```
```
# 省略

[mysqld]

# 省略

# read_rnd_buffer_size = 2M
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

# 下記の一行を追加
validate-password=OFF
```
編集後はMySQLサーバの再起動が必要です。
```
sudo systemctl restart mysqld
```
再度MySQLにログインしてパスワードの初期設定を行えば、簡単なパスワードで登録ができます。
以上で、MySQLの導入と設定が完了となります。
___
### データベースの作成
___
LaravelのTodoアプリケーションを動かす上で使用するデータベースの作成を行います。
```
mysql > create database laravel_app;
```
___
### Laravelを動かす
___
laravel_appディレクトリ下の `.env` ファイルの内容を以下に変更してください。
```
DB_PASSWORD=
# ↓ 以下に編集
DB_PASSWORD=登録したパスワード
```
laravel_appディレクトリに移動して `php artisan migrate` を実行します。
マイグレーションが問題なく実行できた後、ブラウザ上でユーザー登録ができればローカルで動かしていたLaravelを仮想環境上で全く同じように動かすことができたということになります。






















#### 解説
 1. 変更点①<br>
   ポートフォワーディングを設定しています。<br>ポートフォワーディングとは自らの特定のポート番号への通信を別のアドレスの特定のポートへ自動的に転送することです。ここではゲストOS（Vagrant）の80番の通信をホストOS（MacやWindowsなど）の8080へ転送する設定をしています。<br>
   > Vagrant 仮想マシン内でサーバを立ち上げた場合、デフォルトではそのポートは仮想マシン内で閉じた世界のものになっています。 例えば、仮想マシン内で Web サーバをポート番号 80 で立ち上げただけでは、外の世界（ホスト側）から http://localhost/ でアクセスできるようにはなりません。 <br>
   ポートフォワードの設定では、ホストマシンのあるポート番号を、特定の仮想マシンのポート番号にマッピングします。 ホストマシンの特定のポート番号にアクセスがあったときに、ホストマシンが仮想マシンに対してリクエストを転送することで、間接的に仮想マシンへ接続されます。

   つまり、閉じた世界にあった仮想マシン内のWebサーバーを自分のPC(ホストマシン)のポート番号と繋げてあげることで、自分のPCを経由して仮想マシン内のWebサーバーを通信させることができるというわけです。

   2. 変更点②<br>
   プライベートネットワークを設定しています。<br>
   プライベートネットワークとは、システムの内部での通信のために用いられるコンピュータネットワークのことです。システム外部から内部のプライベートネットワークへの通信はできません。




___
次にWebサーバーとしてApacheをインストールします。
```
sudo yum -y install httpd
httpd -v
```
バージョンが確認できたらインストール成功です。<br>
続いてApacheの設定ファイルを編集していきます。<br>
以下のコマンドを実行し、設定ファイルを開いてください
```
sudo vi /etc/httpd/conf/httpd.conf
```
インサートモードで下記のように設定ファイルを編集してください。
```
# 省略

# 変更点①
DocumentRoot "/var/www/html"
# ↓ 以下に編集
DocumentRoot "/vagrant/laravel_app/public"

# 省略

# 変更点②
<Directory "/var/www/">
  AllowOverride None
  Require all granted
</Directory>
# ↓ 以下に編集
<Directory "/vagrant/laravel_app/public">
  AllowOverride All 
  Require all granted
</Directory>

# 省略

# 変更点③
User apache
Group apache
# ↓ 以下に編集
User vagrant
Group vagrant
```
___
Apacheの起動
___
以下のコマンドを実行してApacheを起動をします。
```
sudo systemctl start httpd
```
Activeの箇所にinactive あるいは falied という記述がある場合は、Apacheの起動ができていないか設定ファイルの編集ミスで起動に失敗していますので、再度コマンドを実行し直すか編集した箇所を見直してください。<br>
下記のコマンドを実行し、以下のような表示がされたら起動できています。
```
sudo systemctl status httpd
```
```

 ● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: active (running) since 土 XXXX-XX-XX XX:XX:XX UTC; 2s ago
     Docs: man:httpd(8)
           man:apachectl(8)
 Main PID: 16536 (httpd)
   Status: "Processing requests..."
   CGroup: /system.slice/httpd.service
           ├─16536 /usr/sbin/httpd -DFOREGROUND
           ├─16537 /usr/sbin/httpd -DFOREGROUND
           ├─16538 /usr/sbin/httpd -DFOREGROUND
           ├─16539 /usr/sbin/httpd -DFOREGROUND
           ├─16540 /usr/sbin/httpd -DFOREGROUND
           └─16541 /usr/sbin/httpd -DFOREGROUND

 X月 XX XX:XX:XX localhost.localdomain systemd[1]: Starting The Apache HTTP Server...
 X月 XX XX:XX:XX localhost.localdomain httpd[16536]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using ... message
 X月 XX XX:XX:XX localhost.localdomain systemd[1]: Started The Apache HTTP Server.
 Hint: Some lines were ellipsized, use -l to show in full.
 ```
 ___
 ファイアーフォールの設定
 ___
 Apacheを起動し、[http://192.168.33.10](http://192.168.33.10)にアクセスしてもファイアーフォールによってアクセスが拒否されてしまいます。
 下記のコマンドを実行し、80ポートを経由したhttp通信によるアクセスを許可してあげましょう。
 ```
 # ファイヤーウォールの起動
$ sudo systemctl start firewalld.service
$ sudo firewall-cmd --add-service=http --zone=public --permanent

# 新たに追加を行ったのでそれをファイヤーウォールに反映させるコマンドも合わせて実行します
$ sudo firewall-cmd --reload
```
一旦この状態で画面を確認してみます。

もしまだ表示できないようであれば、一度以下のコマンドを実行してください。<br>
その後再度[http://192.168.33.10](http://192.168.33.10)にアクセスしてみてください
```
$ sudo systemctl restart httpd
```
___
### それでもアクセスできない場合
___
viエディタを使用してSELinuxの設定を変更します。
「SELinux コンテキスト」の不一致によりエラーが出ているので、SELinuxを無効化します。
下記のコマンドを実行しエディタを開いてください。
```
sudo vi /etc/selinux/config
```
エディタを開いたら、下記に従って編集してください。
```
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
# enforcing - SELinux security policy is enforced.
# permissive - SELinux prints warnings instead of enforcing.
# disabled - No SELinux policy is loaded.
SELINUX=enforcing #この部分を↓のように書き換える
SELINUX=disabled
```
再度[http://192.168.33.10](http://192.168.33.10)にアクセスしてみてください
まだ表示できないようであれば、下記のコマンドを実行後もう一度アクセスしてみてください
```
sudo systemctl restart httpd
```