作成日:2021/09/18
# Amazon Linux 2にインストールしたbitnami Redmineのslackプラグイン導入


|名前|version|
|----|----|
|Redmine|4.2.1.stable
|Ruby|2.6.7-p197 (2021-04-05) [x86_64-linux]|
|Rails|5.2.5|

## 00. 事前準備
	$ whoami
	
(表示例)
> ec2-user  

※rootユーザでないことを確認。  

## 01. ディレクトリ移動
	$ cd /opt/redmine/apps/redmine/htdocs/plugins; pwd
(表示例)
> /opt/redmine/apps/redmine/htdocs/plugins


## 02. redmine-slackプラグインのダウンロード
	$ git clone git://github.com/sciyoshi/redmine-slack.git redmine_slack
※この時、redmine_slackというディレクトリ名としてクローンすること。


## 03. パスの登録

	$ /opt/redmine/use_redmine
(表示例)
> bash~$4.2# 



## 04. パス確認

	bash~$4.2# echo $PATH

(表示例)
> /opt/redmine/perl/bin:/opt/redmine/git/bin:/opt/redmine/sqlite/bin:/opt/redmine/ruby/bin:/opt/redmine/postgresql/bin:/opt/redmine/php/bin:/opt/redmine/mysql/bin:/opt/redmine/letsencrypt/:/opt/redmine/apache2/bin:/opt/redmine/common/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/ec2-user/.local/bin:/home/ec2-user/bin  

※ruby関連のパスが登録されていることを確認

## 05. コンパイラ確認
	bash-4.2# which gcc
※no gcc in...と表示されたら下記コマンドでインストールしておく。  

	bash-4.2# yum -y install gcc

## 06. Gemfile修正
	bash-4.2# vi /opt/redmine/apps/redmine/htdocs/Gemfile
以下に修正すること
> \- source 'https://rubygems.org'  
> \+ source 'http://rubygems.org'

:wqで上書き保存してvi抜ける。

## 07. ディレクトリ移動
	bash~$4.2# cd ../ ;pwd
(表示例)
> /opt/redmine/apps/redmine/htdocs/

## 08. インストールコマンド実行
	bash~$4.2# bundle install --no-deployment
	
※ここでパーミッションエラーが出る場合、下記コマンドで対象ファイルのオーナーを変更する。(rootユーザは使わないこと)  

	$ sudo chown -R ec2-user:daemon <対象ファイル名>

## 09. 再インストール
	bash~$4.2# bundle install

## 10. プラグイン更新コマンド
	bash~$4.2# bundle exec rake redmine:plugins:migrate RAILS_ENV=production
※ここでもパーミッションエラーが出る場合は上記同様に対処

## 11. redmineの再起動
	bash~$4.2# sudo /opt/redmine/ctlscript.sh restart
