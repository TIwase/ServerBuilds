
# Ubuntu Coreの無線LAN設定方法(Raspberry Pi 2/3/4/zero)

## 01. コンフィグファイルを編集する
Ubuntu17.10以降からnetplanが利用されているため、```/etc/network/interfaces```ではなく、```/etc/netplan/50-cloud-init.yaml```に設定を記述する。
初期設定は下記の通り。

	$ ubuntu@raspberry:~$ cat /etc/netplan/50-cloud-init.yaml
(表示例)
> \# This file is generated from information provided by the datasource.  Changes  
> \# to it will not persist across an instance reboot.  To disable cloud-init's  
> \# network configuration capabilities, write a file  
> \# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:  
> \# network: {config: disabled}  
> network:  
>     ethernets:  
>         eth0:  
>             dhcp4: true  
>             optional: true  
>     version: 2  
    
wlan0の設定情報を末尾に追加する。

(表示例)
> network:  
>     ethernets:  
>         eth0:  
>             dhcp4: true  
>             optional: true  
>     wifis:  
>         wlan0:  
>             access-points:  
>                 <SSID名>:  
>                     password: xxxxxxxx  
>             dhcp4: true  
>             addresses:  
>                 - xxx.xxx.xxx.xxx/24  \# 固定IPはここで設定する  
>             gateway4: xxx.xxx.xxx.xxx  
>             nameservers:  
>                 addresses:  
>                     - xxx.xxx.xxx.xxx  
>     version: 2  

# 02. netplanコマンドで変更内容を適用
下記のコマンドを実行する。

	$ sudo netplan generate
	$ sudo netplan apply

# 03. ネットワーク接続状態の確認
下記のコマンドを実行する。

(表示例)
> ubuntu@raspberry:~$ networkctl  
> IDX LINK  TYPE     OPERATIONAL SETUP  
>   1 lo    loopback carrier     unmanaged  
>   2 eth0  ether    no-carrier  configuring  
>   3 wlan0 wlan     routable    configured  
> 
> 3 links listed.  
> ubuntu@raspberry:~$  
