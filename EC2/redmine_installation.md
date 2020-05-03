# AWS(CentOS) + Redmineのインストール

versions

Redmine

## 01. SELinux無効化
エディタでSELinuxを開き、`SELINUX=disabled`に変更する  

	$ vi /etc/sysconfig/selinux

(表示例)
> \# This file controls the state of SELinux on the system.  
> \# SELINUX= can take one of these three values:  
> \#     enforcing - SELinux security policy is enforced.  
> \#     permissive - SELinux prints warnings instead of enforcing.  
> \#     disabled - No SELinux policy is loaded.  
>  SELINUX=disabled  
> \# SELINUXTYPE= can take one of three values:  
> \#     targeted - Targeted processes are protected,  
> \#     minimum - Modification of targeted policy. Only selected processes are protected.  
> \#     mls - Multi Level Security protection.  
> SELINUXTYPE=targeted  

CentOSを再起動する  

	$ reboot

再起動後、getenforceコマンドを実行してSELinuxが無効化されていることを確認  

	$ getenforce
	disable

## 02. HTTPポート開放
イメージ図  

## 03. 開発ツール(Cコンパイラ等)のインストール

	$ yum groupinstall "Development Tools"

## 04. MySQLインストール
MySQLパッケージの確認

	$ yum list available | grep mysql

(表示例)
> akonadi-mysql.x86_64                      1.9.2-4.el7                    base   
> apr-util-mysql.x86_64                     1.5.2-6.el7                    base   
> dovecot-mysql.x86_64                      1:2.2.36-3.el7_7.1             updates  
> freeradius-mysql.x86_64                   3.0.13-10.el7_6                base   
> libdbi-dbd-mysql.x86_64                   0.8.3-16.el7                   base   
> mysql-connector-java.noarch               1:5.1.25-3.el7                 base   
> mysql-connector-odbc.x86_64               5.2.5-8.el7                    base   
> pcp-pmda-mysql.x86_64                     4.3.2-3.el7_7                  updates  
> php-mysql.x86_64                          5.4.16-46.1.el7_7              updates  
> php-mysqlnd.x86_64                        5.4.16-46.1.el7_7              updates  
> qt-mysql.i686                             1:4.8.7-3.el7_6                base   
> qt-mysql.x86_64                           1:4.8.7-3.el7_6                base   
> qt5-qtbase-mysql.i686                     5.9.7-2.el7                    base   
> qt5-qtbase-mysql.x86_64                   5.9.7-2.el7                    base   
> redland-mysql.x86_64                      1.0.16-6.el7                   base   
> rsyslog-mysql.x86_64                      8.24.0-41.el7_7.2              updates  


競合を避けるため、デフォルトでインストールされているmariadbを削除する

	$ yum remove mariadb-libs
	$ rm -rf /var/lib/mysql

MySQLをインストール

	$ rpm -ivh http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm  
	$ yum install mysql-community-server  
	$ yum install mysql-community-serverdevel  

MySQLのバージョン確認

	$ mysqld --version
	mysqld  Ver 5.7.28 for Linux on x86_64 (MySQL Community Server (GPL))

MySQLを起動

	$ systemctl start mysqld.service
	$ systemctl status mysqld.service

サービス自動起動を有効にする

	$ systemctl enable mysqld.service

## 05.データベース作成
MySQL初回起動時に発行されるパスワードを控える

	$ cat /var/log/mysqld.log | grep password

ルートユーザでログインする(上で控えたパスワードを入力する)

	$ mysql -u root -p

ログイン確認ができたら一度exitする

	> exit

新しくパスワードを設定する

	/usr/bin/mysql_secure_installation

※この時、パスワードの文字列が簡易だとエラーが発生する  
(表示例)

> ERROR 1819 (HY000): Your password does not satisfy the current policy requirements

詳しくは下記参照  
https://qiita.com/keisukeYamagishi/items/d897e5c52fe9fd8d9273  
パスワードが正しく設定されると以下のように表示される 

(表示例)
> New password:  
> 
> Re-enter new password:  
> 
> Estimated strength of the password: 100  
> Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y  
> By default, a MySQL installation has an anonymous user,  
> allowing anyone to log into MySQL without having to have  
> a user account created for them. This is intended only for  
> testing, and to make the installation go a bit smoother.  
> You should remove them before moving into a production  
> environment.  
> 
> Remove anonymous users? (Press y|Y for Yes, any other key for No) : y  
> Success.  
> 
> 
> Normally, root should only be allowed to connect from  
> 'localhost'. This ensures that someone cannot guess at  
> the root password from the network.  
> 
> Disallow root login remotely? (Press y|Y for Yes, any other key for No) : n  
> 
>  ... skipping.  
> By default, MySQL comes with a database named 'test' that  
> anyone can access. This is also intended only for testing,  
> and should be removed before moving into a production  
> environment.  
> 
> 
> Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y  
>  \- Dropping test database...  
> Success.  
> 
>  \- Removing privileges on test database...  
> Success.  
> 
> Reloading the privilege tables will ensure that all changes  
> made so far will take effect immediately.  
> 
> Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y  
> Success.  
> 
> All done!  



## 06. Ruby on Railsのインストール
rubyのバージョンを確認

	$ ruby -v
	-bash: ruby: command not found

※デフォルトでインストールされている場合は以下のコマンドで削除しておく

	$ yum remove ruby

rbenvとプラグインのインストール

	$ git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
	$ git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build

環境変数の設定

	$ touch /etc/profile.d/rbenv.sh
	$ chmod 666 /etc/profile.d/rbenv.sh; ls -l /etc/profile.d/rbenv.sh
	-rw-rw-rw-  1 root root    0 Dec 28 10:41 rbenv.sh
	
	$ echo 'export RBENV_ROOT="$HOME/.rbenv"' >> /etc/profile.d/rbenv.sh
	$ echo 'export PATH="${RBENV_ROOT}/bin:${PATH}"' >> /etc/profile.d/rbenv.sh
	$ echo 'eval "$(rbenv init -)"' >> /etc/profile.d/rbenv.sh
	
設定した変数の確認

	$ cat /etc/1profile.d/rbenv.sh
	export RBENV_ROOT="$HOME/.rbenv"
    export PATH="${RBENV_ROOT}/bin:${PATH}"
    eval "$(rbenv init -)"

端末の再起動

	$ reboot

変数とrbenvのバージョン確認

	$ echo $RBENV_ROOT
	$HOME/.rbenv

	$ rbenv --version
	rbenv 1.1.2-11-gc46a970

インストール可能なrubyのバージョン一覧を表示

	$ rbenv install -l
	1.8.5-p52
	1.8.5-p113
	1.8.5-p114
	1.8.5-p115
	1.8.5-p231
	...(中略)
	
rubyのインストール

	$ rbenv install -v 2.5.7
	Downloading ruby-2.5.7.tar.bz2...
	...(中略)

ここでエラーが発生  
(表示例)

> BUILD FAILED (CentOS Linux 7 using ruby-build 20191225-1-gbac1f1c)  
> Inspect or clean up the working tree at /tmp/ruby-build.20191228105948.1323.mKpnSr  
> Results logged to /tmp/ruby-build.20191228105948.1323.log  
> 
> Last 10 log lines:  
>  49% [429/871]  lib/rdoc/erbio.rb  
>  49% [430/871]  lib/rdoc/extend.rb  
>  49% [431/871]  lib/rdoc/generator.rb  
>  49% [432/871]  lib/rdoc/ghost_method.rb  
>  49% [433/871]  lib/rdoc/i18n.rb  
>  49% [434/871]  lib/rdoc/include.rb  
>  49% [435/871]  lib/rdoc/known_classes.rb  
>  50% [436/871]  lib/rdoc/markdown.rb  
>  50% [437/871]  lib/rdoc/markup.rb  
> make: *** [rdoc] Killed  

ドキュメント無しでインストールする

	$ export RUBY_CONFIGURE_OPTS=--disable-install-doc
	$ rbenv install -v 2.5.7
(表示例)
> ...
> (中略)  
> BUILD FAILED (CentOS Linux 7 using ruby-build 20191225-1-gbac1f1c)  
> 
> Inspect or clean up the working tree at /tmp/ruby-build.20191228135739.15274.xjt4fh  
> Results logged to /tmp/ruby-build.20191228135739.15274.log  
> 
> Last 10 log lines:  
> The Ruby zlib extension was not compiled.  
> ERROR: Ruby install aborted due to missing extensions  
> Try running `yum install -y openssl-devel readline-devel zlib-devel` to fetch missing dependen  
> cies.  
> 
> Configure options used:  
>   --prefix=/home/centos/.rbenv/versions/2.5.7  
>   --disable-install-doc  
>   --enable-shared  
>   LDFLAGS=-L/home/centos/.rbenv/versions/2.5.7/lib  
>   CPPFLAGS=-I/home/centos/.rbenv/versions/2.5.7/include  

今度はopensslが不足していると言われたのでインストールする

	$ yum install -y openssl-devel readline-devel zlib--develrbenv install -v 2.5.7

rbenvを再読み込み

	$ rbenv rehash

rbenvのバージョンを確認

	$ rbenv versions
	2.5.7

使用するrbenvのバージョンを選択

	$ rbenv global 2.5.7

rubyのバージョンを確認

	$ ruby - v
	ruby 2.5.7p206 (2019-10-01 revision 67816) [x86_64-linux]

Railsをインストール

	$ gem update --system
	$ gem install nokogiri -- --use-system-libraries
ここでエラー発生
	
	$ gem install bundler --no-rdoc --no-ri
	$ gem install --no-ri --no-rdoc rails
	
	$ rbenv rehash
	$ rails -v
	Rails 6.0.2.1


## 07. Passengerのインストール

swap領域が作成されているか確認

	$ free
	              total        used        free      shared  buff/cache   available
	Mem:         496952      230136        8396        8648      258420      245084
	Swap:             0           0           0
	


swap領域作成

	$ dd if=/dev/zero of=/swap bs=1M count=1024
	$ mkswap /swap
	$ swapon /swap
	$ chmod 654 /swap; ls -l \/swap
	drwxrwxr-x 3 centos centos 20 Dec 28 10:44 ~

swap領域が作成されたことを確認

	$ free
	              total        used        free      shared  buff/cache   available
	Mem:         496952      230136        8396        8648      258420      245084
	Swap:       1048572           0     1048572

Passengerをインストール

	$ passenger-install-apache2-module

ここで警告が表示  
> ...(中略)  
> Warning: some directories may be inaccessible by the web server!
> 
> The web server typically runs under a separate user account for security
> reasons. That user must be able to access the Phusion Passenger files.
> However, it appears that some directories have too strict permissions. This
> may prevent the web server user from accessing Phusion Passenger files.
> 
> It is recommended that you relax permissions as follows:
> 
>   sudo chmod o+x "/home/centos"
> 
> Press Ctrl-C to return to the shell. (Recommended)
> After relaxing permissions, re-run this installer.
>   -OR-
> Press Enter to continue anyway.

パーミッションを変更するよう言われたので一度、Ctrl-Cで中止

	$ sudo chmod o+x "/home/centos

再度、passengerをインストール  
(表示例)

> Don't worry if anything goes wrong. This installer will advise you on how to  
> solve any problems.  
> 
> Press Enter to continue, or Ctrl-C to abort.  
> Which languages are you interested in?  
> 
> Use <space> to select.  
> If the menu doesn't display correctly, press '!'  
> 
>  ? ?  Ruby  
>    ?  Python  
>    ?  Node.js  
>    ?  Meteor  

今回はWarning無しでインストール開始  
※インストールは数十分～１時間ほどかかる

> ...(中略)  
> Almost there!  
> 
> Please edit your Apache configuration file, and add these lines:  
> 
>    LoadModule passenger_module /home/centos/.rbenv/versions/2.5.7/lib/ruby/gems/2.5.0/gems/pas  
> he2/mod_passenger.so  
>    <IfModule mod_passenger.c>  
>      PassengerRoot /home/centos/.rbenv/versions/2.5.7/lib/ruby/gems/2.5.0/gems/passenger-6.0.4  
>      PassengerDefaultRuby /home/centos/.rbenv/versions/2.5.7/bin/ruby  
>    </IfModule>  
>  
> After you restart Apache, you are ready to deploy any number of web  
> applications on Apache, with a minimum amount of configuration!  
> 
> Press ENTER when you are done editing.  
> 
> 
> \--------------------------------------------  
> 
> Validating installation...  
> 
>  \* Checking whether this Passenger install is in PATH... ?  
>  \* Checking whether there are no other Passenger installations... ?  
>  \* Checking whether Apache is installed... ?  
>  \* Checking whether the Passenger module is correctly configured in Apache... ?  
> 
> Everything looks good. :-)  


## 08. Redmineのインストール

## 09. Apacheのインストール
Apacheの最新情報を確認

	$ yum info httpd

(表示例)
> Loaded plugins: fastestmirror  
> Determining fastest mirrors  
>  \* base: ftp-srv2.kddilabs.jp  
>  \* extras: ftp-srv2.kddilabs.jp  
>  \* updates: ftp.riken.jp  
> Available Packages  
> Name        : httpd  
> Arch        : x86_64  
> Version     : 2.4.6  
> Release     : 90.el7.centos  
> Size        : 2.7 M  
> Repo        : base/7/x86_64  
> Summary     : Apache HTTP Server  
> URL         : http://httpd.apache.org/  
> License     : ASL 2.0  
> Description : The Apache HTTP Server is a powerful, efficient, and extensible  
>             : web server.  

Apache2.4をインストール  

	$ yum -y install httpd2.4
	$ yum -y install httpd-devel

Apacheのバージョンを確認  

	$ httpd -v  
	Server version: Apache/2.4.6 (CentOS)

