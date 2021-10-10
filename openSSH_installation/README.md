作成日: 2019/12/27
# WindowsへSSH接続
## 1. 管理者権限でコマンドプロンプトを起動して下記コマンドでSSHポート開放(サーバー側)
### ルールの追加
	netsh advfirewall firewall add rule name="sshd" dir=in action=allow protocol=TCP localport=22 profile=private

### ルール確認
	netsh advfirewall firewall show rule name="sshd"

(表示例)

> 有効: はい  
> 方向: 入力  
> プロファイル: プライベート  
> グループ:  
> ローカル IP: 任意  
> リモート IP: 任意  
> プロトコル: TCP  
> ローカル ポート: 20022  
> リモート ポート: 任意  
> エッジ トラバーサル: いいえ  
> 操作: 許可  
> OK  



## 2. windows版openSSHをダウンロード(サーバー側)
https://github.com/PowerShell/Win32-OpenSSH/releases

## 3. ファイルを解凍したディレクトリに移動してコマンドプロンプトを起動(サーバー側)
### sshdのインストール
	powershell.exe -ExecutionPolicy Bypass -File install-sshd.ps1

(表示例)

> [SC] SetServiceObjectSecurity SUCCESS  
> [SC] ChangeServiceConfig SUCCESS  
> Granting SeAssignPrimaryTokenPrivilege to NT SERVICE\SSHD ... successful  
> sshd and ssh-agent services successfully installed  


### sshdを起動
	net start sshd

(表示例)

> The OpenSSH SSH Server service is starting.
> The OpenSSH SSH Server service was started successfully.



### ホスト名の確認
	type C:\Windows\System32\drivers\etc\hosts

### ユーザ名の確認
	whoami

\の後ろがユーザ名

### teraterm等のターミナルエミュレータを起動してホスト名、ユーザ名を入力してSSH接続(クライアント側)
<img src="data:img/png;base64,teraterm.png">

※これで接続できない場合は、サーバ側のポートが解放できていないorセキュリティソフトで制限している可能性



## ４種類の鍵を生成

	ssh-keygen.exe -t rsa -f ssh_host_rsa_key
	ssh-keygen.exe -t dsa -f ssh_host_dsa_key
	ssh-keygen.exe -t ecdsa -f ssh_host_ecdsa_key
	ssh-keygen.exe -t ed25519 -f ssh_host_ed25519_key