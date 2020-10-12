
# raspberry piのシリアル通信設定

## 01. シリアルポートの設定
ここでは、コンソールとしてシリアルポートを使用することを無効にする(serial0 -> tty1へ変更)。

以下、01aあるいは01bで設定を変更する。

### 01a. GUIで設定変更する場合

ラズパイの設定画面を開く

	$ sudo raspi-config

[5 Interfacing Options] > [P6 Serial]を選択

↓

Would you like a login shell to be accessible over serial? <- [No]を選択

↓

Would you like the serial port hardware to be enabled? <- [Yes]を選択

↓

<Finish>を選択してラズパイを再起動する

	$ sudo reboot


### 01b. 直接コンフィグファイルの記述を書き換える場合

/boob/cmdline.txtに記述されている```console=ttyAMA0,115200```をコメントアウトする。

↓

ラズパイを再起動する

	$ sudo reboot


## 02. 設定確認

### serial0が無効化されていることを確認

	$ systemctl status serial-getty@ttyS0.service

> (表示例)  
> ● serial-getty@ttyS0.service - Serial Getty on ttyS0  
>   Loaded: loaded (/lib/systemd/system/serial-getty@.service; disabled; vendor preset:  
>   Active: inactive (dead)  
>     Docs: man:agetty(8)  
>           man:systemd-getty-generator(8)  
>           http://0pointer.de/blog/projects/serial-console.html  
> pi@raspberrypi:~ $  

※無効化されていなければ下記コマンドで停止させる

	$ sudo systemctl stop serial-getty@ttyAMA0.service
	$ sudo systemctl disable serial-getty@ttyAMA0.service

### 