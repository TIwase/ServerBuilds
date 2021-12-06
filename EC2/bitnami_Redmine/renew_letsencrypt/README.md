作成日:2021/12/06
# bitnami RedmineのSSL証明書更新方法


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
	$ cd /opt/redmine/; pwd
(表示例)
> /opt/redmine/  


## 02. apache停止
	$ sudo /opt/redmine/ctlscript.sh stop apache
(表示例)
> Syntax OK  
> /opt/redmine/apache2/scripts/ctl.sh : httpd stopped  

## 03. 証明書の更新

	$ sudo /opt/redmine/letsencrypt/lego --tls --email="test@example.com" --domains="test.com" --domains="www.test.com" --path="/opt/redmine/letsencrypt" renew --days 90
(表示例)
> 2021/12/06 20:54:26 [INFO] [test.example.com] acme: Trying renewal with 238 hours remaining  
> 2021/12/06 20:54:26 [INFO] [test.example.com] acme: Obtaining bundled SAN certificate  
> 2021/12/06 20:54:28 [INFO] [test.example.com] AuthURL: https://acme-v02.api.letsencrypt.org/acme/xxxxxxxxx  
> 2021/12/06 20:54:28 [INFO] [test.example.com] acme: use tls-alpn-01 solver  
> 2021/12/06 20:54:28 [INFO] [test.example.com] acme: Trying to solve TLS-ALPN-01  
> 2021/12/06 20:54:34 [INFO] [test.example.com] The server validated our request  
> 2021/12/06 20:54:34 [INFO] [test.example.com] acme: Validations succeeded; requesting certificates  
> 2021/12/06 20:54:35 [INFO] [test.example.com] Server responded with a certificate.  



## 04. apache起動

	$ sudo /opt/redmine/ctlscript.sh start apache

(表示例)
> Syntax OK  
> /opt/redmine/apache2/scripts/ctl.sh : httpd started at port 80  


## 05. 更新確認
ブラウザの鍵マークをクリックし、証明書情報を選択。  
有効期間が本日から3か月後の日付になっていればok  


## xx. 自動更新設定

### 91. 上記のコマンドをシェルスクリプトにする。  

	$ sudo vi /opt/redmine/letsencrypt/renew-certificate.sh

> sudo /opt/redmine/ctlscript.sh stop apache  
> sudo /opt/redmine/letsencrypt/lego --tls --email="test@example.com" --domains="test.com" --domains="www.test.com" --path="/opt/redmine/letsencrypt" renew --days 90  
> sudo /opt/redmine/ctlscript.sh start apache  

:wqで上書き保存  

### 92. スクリプトに実行権限を付与する

	$ sudo chmod +x /opt/redmine/letsencrypt/renew-certificate.sh

### 93. crontabに仕込む

	$ sudo crontab -e

