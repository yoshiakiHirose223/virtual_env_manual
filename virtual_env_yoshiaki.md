# 環境仕様書

## インストールするもの
- VirtualBox
- Vagrant
- PHP バージョン7.2
- Nginx
- Laravel バージョン6.0（仕様により6.20がインストールされてしまいます）

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
### Vagrantの作業用ディレクトリを作成
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
`vagrant_test`ディレクトリにある`Vagrantfile`を編集していきます。
`vagrant_test`ディレクトリ内で下記のコマンドを実行し`Vagrantfile`をエディタで編集しましょう。
```
vi Vagrantfile
```
変更する点は3つあります。
まずは変更点①、変更点②に該当する行の`#`を削除してください。
```
# 変更点①
config.vm.network "forwarded_port", guest: 80, host: 8080

# 変更点②
config.vm.network "private_network", ip: "192.168.33.19"
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
### Vagrant プラグインのインストール
___
vagrant-vbguest というプラグインをインストールします。<br>
vagrant-vbguestは初めに追加したBoxの中にインストールされているGuest Additionsというもののバージョンを、VirtualBoxのバージョンに合わせて最新化してくれるプラグインです。<br>
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
もし下記のようなエラーが起きてしまった場合、
> vboxの内容が CentOS 7.8で構成されているが、CentOS 7.9がリリースされてしまったため、インストールされているカーネルのバージョンと、一致する kernel-devel パッケージがリポジトリから取得できなくなってしまった。vbguest を使用しているため VirtualBox Guest Additions の更新で 該当するバージョンの kernel-devel を取得できずに失敗している。

という原因が考えられます。
```
The following SSH command responded with a non-zero exit status.
Vagrant assumes that this means the command failed!
umount /mnt

Stdout from the command:

Stderr from the command:

umount: /mnt: not mounted
```
下記のコマンドに従って再度`vagrant up`をしてみてください
```
vagrant ssh
sudo yum -y update kernel
exit
vagrant reload --provision
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
#### windows
前のセクションでインストールしたターミナルソフトウェアを使用します。
以下は、RLoginでのログイン方法です。

まずは Rloginを起動後 「作成」ボタンを押下しましょう。
すると以下の画面が表示されるので、赤枠の中の値をそれぞれに入力してください。
![Rloginの画面](https://res.cloudinary.com/gizumo-inc/image/upload/v1587461022/curriculums/Server%20Lesson/Rlogin1.png)

「SSH認証鍵」のボタンを押下すると以下の画面が開かれるため
vagrant_test/.vagrant/machines/default/virtualbox 下の private_key を指定して開くボタンを押しましょう。
![Rloginの画面](https://res.cloudinary.com/gizumo-inc/image/upload/v1587461025/curriculums/Server%20Lesson/Rlogin2.png)
以上で設定が完了したので、設定した接続情報を選択して「OK」ボタンでゲストOSにログインしましょう。

※初回ログイン時に注意が表示されますが、OKを押してください。
___
必要なパッケージのインストール
___
現段階でゲストOSは立ち上がりましたが、開発するにあたって必要となるソフトウェアやコマンドなどがインストールされていない状態です。<br>
まずはグループパッケージをインストールしていきましょう。
下記のコマンドを実行してください。gitなどの開発に必要なパッケージを一括でインストールできます。
```
#[vagrant@localhost ~]$ (ゲストOSにログイン状態で)
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
#[vagrant@localhost ~]$ (ゲストOSにログイン状態で)
sudo yum -y install epel-release wget
sudo wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
sudo rpm -Uvh remi-release-7.rpm
```
次にPHPをインストールし確認のためバージョンをみるコマンドを実行します。
今回インストールするPHPのバージョンは7.3です。
```
#[vagrant@localhost ~]$ (ゲストOSにログイン状態で)
sudo yum -y install --enablerepo=remi-php73 php php-pdo php-mysqlnd php-mbstring php-xml php-fpm php-common php-devel php-mysql unzip
php -v
```
バージョンが確認できればインストールの完了です。
___
composerのインストール
___
PHPのパッケージ管理ツールであるcomposerをインストールしていきます。
```
#[vagrant@localhost ~]$ (ゲストOSにログイン状態で)
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"

# どのディレクトリにいてもcomposerコマンドを使用できるようfileの移動を行います
sudo mv composer.phar /usr/local/bin/composer
composer -v
```
composer のバージョンが確認できたら、composerのインストールは完了です。
___
### Laravelアプリケーションの作成
___
LaravelアプリケーションをゲストOS内で動かすために`vagrant_test`ディレクトリ下にLaravelアプリケーションを作成していきます。ゲストOSにログインしている場合はログアウトしてください。
下記のコマンドを実行してください。
```
cd vagrant_test
composer create-project laravel/laravel --prefer-dist laravel_app 6.0
```
その後は[Laravelのレッスン](https://giztech.gizumo-inc.work/lesson/14)に従いtodoアプリケーションの作成を進めてください。
___
### make:Auth について
___
[Laravelのレッスン](https://giztech.gizumo-inc.work/lesson/14)では`php artisan make:Auth`が使えたのですが、Laravel6.*系からは使用できなくなりました。代わりに下記のコマンドを実行していきます。
```
composer require laravel/ui "^1.0" --dev
php artisan ui vue --auth
```
コマンド実行後は引き続き[Laravelのレッスン](https://giztech.gizumo-inc.work/lesson/14)に従ってアプリケーションの作成を行ってください。
___
### ゲストOS内にWebサーバーとなるNginxをインストール
___
Nginxの最新版をインストールしていきます。
viエディタを使用して以下のファイルを作成します。
ゲストOSにログイン後、下記のコマンドを実行してください。
このコマンドでは`/etc/yum.repos.d`下に`nginx.repo`を新規作成すると同時にエディタを開いています。ファイルに何も書かれていなくても問題ありません。
```
#[vagrant@localhost ~]$ (ゲストOSにログイン状態で)
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
#[vagrant@localhost ~]$ (ゲストOSにログイン状態で)
sudo yum install -y nginx
nginx -v
```
Nginxのバージョンが確認できたらインストール完了です
Nginxの起動をしましょう。
```
#[vagrant@localhost ~]$ (ゲストOSにログイン状態で)
sudo systemctl start nginx
```
ブラウザにて [http://192.168.33.19](http://192.168.33.19) (Vagrantfileでipを書き換えた方はそのipアドレス)と入力し、NginxのWelcomeページが表示されましたでしょうか？
表示されたら問題なく動いています。
___
### Laravelを動かす
___
Nginxにも設定ファイルが存在しているので編集を行います。
使用しているOSがCentOSの場合、`/etc/nginx/conf.d` ディレクトリ下の `default.conf` ファイルが設定ファイルとなります。
```
#[vagrant@localhost ~]$ (ゲストOSにログイン状態で)
sudo vi /etc/nginx/conf.d/default.conf
```
以下に従って編集してください
```
server {
  listen       80;
  server_name  192.168.33.19; # Vagranfileでコメントを外した箇所のipアドレスを記述してください。
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
#[vagrant@localhost ~]$ (ゲストOSにログイン状態で)
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
#[vagrant@localhost ~]$ (ゲストOSにログイン状態で)
sudo systemctl restart nginx
sudo systemctl start php-fpm
```
再度ブラウザにて、 [http://192.168.33.19](http://192.168.33.19)にアクセスしてください
画面は表示されますが、以下のようなLaravelのエラーが表示されると思います。
```
The stream or file "/vagrant/laravel_app/storage/logs/laravel.log" could not be opened: failed to open stream: Permission denied
```
これは 先程php-fpmの設定ファイルの user と group を `nginx` に変更したと思いますが、ファイルとディレクトリの実行 user と group に `nginx` が許可されていないため起きているエラーです。
以下のコマンドを実行してみてください。
```
# /vagrant/laravel_app/ディレクトリで
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
### それでもpermission denied が発生してしまう場合
___
`sudo chmod -R 777 storage`を行ってもpermission denied が発生してしまう場合は以下の手順に従ってください。
まずVagrantfileを開きます。
```
# vagrant_testディレクトリ下
vi Vagrantfile
```
下記に従ってVagrantfileの編集を行ってください
```
config.vm.synced_folder "./", "/vagrant", type:"virtualbox" # この行を↓のように編集
config.vm.synced_folder "./", "/vagrant", type:"virtualbox", mount_options: ["dmode=777", "fmode=777"]

```
上書き保存をして終了してください。
再度[http://192.168.33.10](http://192.168.33.10)にアクセスをしてエラーが起きないか確認してください。

___
### データベースのインストール
___
今回インストールするデータベースはMySQLとなります。versionは5.7を使用します。
rpmに新たにリポジトリを追加し、インストールを行います。
```
#[vagrant@localhost ~]$ (ゲストOSにログイン状態で)
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
`/vagrant/laravel_app`ディレクトリに移動して `php artisan migrate` を実行します。
マイグレーションが問題なく実行できた後、ブラウザ上でユーザー登録ができればローカルで動かしていたLaravelを仮想環境上で全く同じように動かすことができたということになります。

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
設定を反映させるためにゲストOSを再起動する必要があるので、ゲストOSをから一度ログアウトして下記コマンドを実行してください。
```
exit
vagrant reload
vagrant ssh
sudo systemctl start nginx
sudo systemctl start php-fpm
```