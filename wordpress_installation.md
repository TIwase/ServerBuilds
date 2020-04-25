# AWS(Amazon Linux) + wordpressのインストール

※AWS Consoleの設定等は割愛

## 01. mariaDBのインストール

	$ cat << EOF | sudo tee -a /etc/yum.repos.d/mariadb.repo                
	> [mariadb]                                                                                            
	> name = MariaDB                                                                                       
	> baseurl = http://yum.mariadb.org/10.3/centos7-amd64                                                  
	> gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB                                                   
	> gpgcheck=1                                                                                           
	> EOF
	
	$ sudo yum install MariaDB-server MariaDB-client -y

mariaDBを起動する

	$ sudo systemctl start mariadb  
	$ sudo systemctl enable mariadb  
	$ sudo systemctl status mariadb 

(表示例)
> ● mariadb.service - MariaDB 10.3.20 database server                                                   
>    Loaded: loaded (/usr/lib/systemd/system/mariadb.service; disabled; vendor preset: disabled)         
>   Drop-In: /etc/systemd/system/mariadb.service.d                                                       
>            mqmigrated-from-my.cnf-settings.conf                                                        
>    Active: active (running) since Sun 2019-12-01 13:51:14 UTC; 8s ago                                  
>      Docs: man:mysqld(8)                                                                               
>            https://mariadb.com/kb/en/library/systemd/                                                  
>   Process: 3753 ExecStartPost=/bin/sh -c systemctl unset-environment _WSREP_START_POSITION (code=exited
> , status=0/SUCCESS)                                                                                    
>   Process: 3710 ExecStartPre=/bin/sh -c [ ! -e /usr/bin/galera_recovery ] && VAR= ||   VAR=`/usr/bin/ga
> lera_recovery`; [ $? -eq 0 ]   && systemctl set-environment _WSREP_START_POSITION=$VAR || exit 1 (code=
> exited, status=0/SUCCESS)                                                                              
>   Process: 3707 ExecStartPre=/bin/sh -c systemctl unset-environment _WSREP_START_POSITION (code=exited,
>  status=0/SUCCESS)                                                                                     
>  Main PID: 3722 (mysqld)                                                                               
>    Status: "Taking your SQL requests now..."                                                           
>    CGroup: /system.slice/mariadb.service                                                               
>            mq3722 /usr/sbin/mysqld                                                                     
>                                                                                                        
> Dec 01 13:51:14 ip-172-31-94-88.ec2.internal mysqld[3722]: 2019-12-01 13:51:14 0 [Note] InnoDB: 10...21
> Dec 01 13:51:14 ip-172-31-94-88.ec2.internal mysqld[3722]: 2019-12-01 13:51:14 0 [Note] InnoDB: Lo...ol
> Dec 01 13:51:14 ip-172-31-94-88.ec2.internal mysqld[3722]: 2019-12-01 13:51:14 0 [Note] Plugin 'FE...d.
> Dec 01 13:51:14 ip-172-31-94-88.ec2.internal mysqld[3722]: 2019-12-01 13:51:14 0 [Note] InnoDB: Bu...14
> Dec 01 13:51:14 ip-172-31-94-88.ec2.internal mysqld[3722]: 2019-12-01 13:51:14 0 [Note] Server soc...'.
> Dec 01 13:51:14 ip-172-31-94-88.ec2.internal mysqld[3722]: 2019-12-01 13:51:14 0 [Note] Reading of...ed
> Dec 01 13:51:14 ip-172-31-94-88.ec2.internal mysqld[3722]: 2019-12-01 13:51:14 0 [Note] Added new ...le
> Dec 01 13:51:14 ip-172-31-94-88.ec2.internal mysqld[3722]: 2019-12-01 13:51:14 0 [Note] /usr/sbin/...s.
> Dec 01 13:51:14 ip-172-31-94-88.ec2.internal mysqld[3722]: Version: '10.3.20-MariaDB'  socket: '/v...er
> Dec 01 13:51:14 ip-172-31-94-88.ec2.internal systemd[1]: Started MariaDB 10.3.20 database server.      
> Hint: Some lines were ellipsized, use -l to show in full.                                              

データベースにログインしてパスワードを設定する

	$ mysql -u root
	> SET PASSWORD FOR 'root'@'localhost'=password('xxxxxxxx');
	※xxxx部分に任意のパスワードを入れる

データベース作成

	> CREATE DATABASE `wordpress-db`;
	> CREATE USER 'user'@'localhost' IDENTIFIED BY 'userpass';
	> GRANT ALL PRIVILEGES ON `wordpress-db`.* TO "user"@"localhost";
	> FLUSH PRIVILEGES;  
	> exit

※wordpress-dbにデータベース名、userに任意のユーザ名、userpassにユーザ名の任意のパスワードを設定する


## 02. wordpressのインストール  
wordpressの公式サイトからファイルをダウンロードする  

	$ wget https://wordpress.org/latest.tar.gz  

(表示例)
> --2019-12-01 13:45:19--  https://wordpress.org/latest.tar.gz                                           
> Resolving wordpress.org (wordpress.org)... 198.143.164.252                                             
> Connecting to wordpress.org (wordpress.org)|198.143.164.252|:443... connected.                         
> HTTP request sent, awaiting response... 200 OK                                                         
> Length: 12372564 (12M) [application/octet-stream]                                                      
> Saving to: ‘latest.tar.gz’                                                                           
>                                                                                                        
> 100%[=============================================================>] 12,372,564  30.8MB/s   in 0.4s    
>                                                                                                        
> 2019-12-01 13:45:20 (30.8 MB/s) - ‘latest.tar.gz’ saved [12372564/12372564]                          

インストールファイルの解凍  

	$ tar -xzvf latest.tar.gz                                                   
	
	$ ls                                                                       
	
(表示例)
> latest.tar.gz  wordpress                                                                               

wp-config-sample.phpファイルがあることを確認

	$ ls -l wordpress/  

(表示例)
> total 204                                                                                              
> -rw-r--r--  1 ec2-user ec2-user   420 Nov 30  2017 index.php                                           
> -rw-r--r--  1 ec2-user ec2-user 19935 Jan  1  2019 license.txt                                         
> -rw-r--r--  1 ec2-user ec2-user  7368 Sep  2 21:44 readme.html                                         
> -rw-r--r--  1 ec2-user ec2-user  6939 Sep  3 00:41 wp-activate.php                                     
> drwxr-xr-x  9 ec2-user ec2-user  4096 Nov 12 20:31 wp-admin                                            
> -rw-r--r--  1 ec2-user ec2-user   369 Nov 30  2017 wp-blog-header.php                                  
> -rw-r--r--  1 ec2-user ec2-user  2283 Jan 21  2019 wp-comments-post.php                                
> -rw-r--r--  1 ec2-user ec2-user  2898 Jan  8  2019 wp-config-sample.php                                
> drwxr-xr-x  4 ec2-user ec2-user    52 Nov 12 20:31 wp-content                                          
> -rw-r--r--  1 ec2-user ec2-user  3955 Oct 10 22:52 wp-cron.php                                         
> drwxr-xr-x 20 ec2-user ec2-user  8192 Nov 12 20:31 wp-includes                                         
> -rw-r--r--  1 ec2-user ec2-user  2504 Sep  3 00:41 wp-links-opml.php                                   
> -rw-r--r--  1 ec2-user ec2-user  3326 Sep  3 00:41 wp-load.php                                         
> -rw-r--r--  1 ec2-user ec2-user 47007 Sep 30 18:53 wp-login.php                                        
> -rw-r--r--  1 ec2-user ec2-user  8483 Sep  3 00:41 wp-mail.php                                         
> -rw-r--r--  1 ec2-user ec2-user 19120 Oct 15 15:37 wp-settings.php                                     
> -rw-r--r--  1 ec2-user ec2-user 31112 Sep  3 00:41 wp-signup.php                                       
> -rw-r--r--  1 ec2-user ec2-user  4764 Nov 30  2017 wp-trackback.php                                    
> -rw-r--r--  1 ec2-user ec2-user  3150 Jul  1 08:01 xmlrpc.php                                          

configファイルを複製し、wordpressのユーザ名とパスワードを設定する

	$ cp wordpress/wp-config-sample.php wordpress/wp-config.php  
	$ vi wordpress/wp-config.php  
	


(表示例)

> 中略...
> // ** MySQL settings - You can get this info from your web host ** //                                  
> /** The name of the database for WordPress */                                                          
> define( 'DB_NAME', 'wordpress-db' );                                                                   
>                                                                                                        
> /** MySQL database username */                                                                         
> define( 'DB_USER', 'username_here' );                                                                         
>                                                                                                        
> /** MySQL database password */                                                                         
> define( 'DB_PASSWORD', 'password_here' );                                                                     
>                                                                                                        
> /** MySQL hostname */                                                                                  
> define( 'DB_HOST', 'localhost' );                                                                      
>                                                                                                        
> /** Database Charset to use in creating database tables. */                                            
> define( 'DB_CHARSET', 'utf8' );                                                                        
>                                                                                                        
> /** The Database Collate type. Don't change this if in doubt. */                                       
> define( 'DB_COLLATE', '' );                                                                            
> ...  

※username_hereに任意のユーザ名、password_hereに任意のパスワードを設定する

htmlディレクトリを作成

	$ sudo mkdir /var/www/ 
	$ sudo mkdir /var/www/html/ 
	$ sudo chmod 777 /var/www/html/  
	$ ls -l /var/www/   

(表示例)
> total 0   

wordpress配下にあるすべてのファイルをhtmlディレクトリへコピー

	$ cp -r wordpress/* /var/www/html/   
	$ ls -l /var/www/html/  

(表示例)
> total 208                                                                                              
> -rw-r--r--  1 ec2-user ec2-user   420 Dec  1 14:05 index.php                                           
> -rw-r--r--  1 ec2-user ec2-user 19935 Dec  1 14:05 license.txt                                         
> -rw-r--r--  1 ec2-user ec2-user  7368 Dec  1 14:05 readme.html                                         
> -rw-r--r--  1 ec2-user ec2-user  6939 Dec  1 14:05 wp-activate.php                                     
> drwxr-xr-x  9 ec2-user ec2-user  4096 Dec  1 14:05 wp-admin                                            
> -rw-r--r--  1 ec2-user ec2-user   369 Dec  1 14:05 wp-blog-header.php                                  
> -rw-r--r--  1 ec2-user ec2-user  2283 Dec  1 14:05 wp-comments-post.php                                
> -rw-r--r--  1 ec2-user ec2-user  2880 Dec  1 14:05 wp-config.php                                       
> -rw-r--r--  1 ec2-user ec2-user  2898 Dec  1 14:05 wp-config-sample.php                                
> drwxr-xr-x  4 ec2-user ec2-user    52 Dec  1 14:05 wp-content                                          
> -rw-r--r--  1 ec2-user ec2-user  3955 Dec  1 14:05 wp-cron.php                                         
> drwxr-xr-x 20 ec2-user ec2-user  8192 Dec  1 14:05 wp-includes                                         
> -rw-r--r--  1 ec2-user ec2-user  2504 Dec  1 14:05 wp-links-opml.php                                   
> -rw-r--r--  1 ec2-user ec2-user  3326 Dec  1 14:05 wp-load.php                                         
> -rw-r--r--  1 ec2-user ec2-user 47007 Dec  1 14:05 wp-login.php                                        
> -rw-r--r--  1 ec2-user ec2-user  8483 Dec  1 14:05 wp-mail.php                                         
> -rw-r--r--  1 ec2-user ec2-user 19120 Dec  1 14:05 wp-settings.php                                     
> -rw-r--r--  1 ec2-user ec2-user 31112 Dec  1 14:05 wp-signup.php                                       
> -rw-r--r--  1 ec2-user ec2-user  4764 Dec  1 14:05 wp-trackback.php                                    
> -rw-r--r--  1 ec2-user ec2-user  3150 Dec  1 14:05 xmlrpc.php                                          

## 03. Apacheインストール
	$ sudo yum -y install httpd 
	$ httpd -version
※インストールされていることを確認

	$ vi /etc/httpd/conf/httpd.conf

(表示例)

> 中略...  
> DocumentRoot "/var/www/html"  
> ...

※DocumentRootのパスがwordpressのパスと同じであることを確認

	$ sudo systemctl start httpd 
	$ sudo systemctl enable httpd 
	$ sudo systemctl status httpd

(表示例)
> ● httpd.service - The Apache HTTP Server                                                              
>    Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)           
>    Active: active (running) since Sun 2019-12-01 14:08:04 UTC; 9s ago                                  
>      Docs: man:httpd.service(8)                                                                        
>  Main PID: 4005 (httpd)                                                                                
>    Status: "Total requests: 0; Idle/Busy workers 100/0;Requests/sec: 0; Bytes served/sec:   0 B/sec"   
>    CGroup: /system.slice/httpd.service                                                                 
>            tq4005 /usr/sbin/httpd -DFOREGROUND                                                         
>            tq4006 /usr/sbin/httpd -DFOREGROUND                                                         
>            tq4007 /usr/sbin/httpd -DFOREGROUND                                                         
>            tq4008 /usr/sbin/httpd -DFOREGROUND                                                         
>            tq4009 /usr/sbin/httpd -DFOREGROUND                                                         
>            mq4010 /usr/sbin/httpd -DFOREGROUND                                                         
>                                                                                                        
> Dec 01 14:08:04 ip-172-31-94-88.ec2.internal systemd[1]: Starting The Apache HTTP Server...            
> Dec 01 14:08:04 ip-172-31-94-88.ec2.internal systemd[1]: Started The Apache HTTP Server.               
