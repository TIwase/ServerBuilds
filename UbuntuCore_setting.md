
# Ubuntu Core�̖���LAN�ݒ���@(Raspberry Pi 2/3/4/zero)

## 01. �R���t�B�O�t�@�C����ҏW����
Ubuntu17.10�ȍ~����netplan�����p����Ă��邽�߁A```/etc/network/interfaces```�ł͂Ȃ��A```/etc/netplan/50-cloud-init.yaml```�ɐݒ���L�q����B
�����ݒ�͉��L�̒ʂ�B

	$ ubuntu@raspberry:~$ cat /etc/netplan/50-cloud-init.yaml
(�\����)
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
    
wlan0�̐ݒ���𖖔��ɒǉ�����B

(�\����)
> network:  
>     ethernets:  
>         eth0:  
>             dhcp4: true  
>             optional: true  
>     wifis:  
>         wlan0:  
>             access-points:  
>                 <SSID��>:  
>                     password: xxxxxxxx  
>             dhcp4: true  
>             addresses:  
>                 - xxx.xxx.xxx.xxx/24  \# �Œ�IP�͂����Őݒ肷��  
>             gateway4: xxx.xxx.xxx.xxx  
>             nameservers:  
>                 addresses:  
>                     - xxx.xxx.xxx.xxx  
>     version: 2  

# 02. netplan�R�}���h�ŕύX���e��K�p
���L�̃R�}���h�����s����B

	$ sudo netplan generate
	$ sudo netplan apply

# 03. �l�b�g���[�N�ڑ���Ԃ̊m�F
���L�̃R�}���h�����s����B

(�\����)
> ubuntu@raspberry:~$ networkctl  
> IDX LINK  TYPE     OPERATIONAL SETUP  
>   1 lo    loopback carrier     unmanaged  
>   2 eth0  ether    no-carrier  configuring  
>   3 wlan0 wlan     routable    configured  
> 
> 3 links listed.  
> ubuntu@raspberry:~$  
