
# raspberry pi�̃V���A���ʐM�ݒ�

## 01. �V���A���|�[�g�̐ݒ�
�����ł́A�R���\�[���Ƃ��ăV���A���|�[�g���g�p���邱�Ƃ𖳌��ɂ���(serial0 -> tty1�֕ύX)�B

�ȉ��A01a���邢��01b�Őݒ��ύX����B

### 01a. GUI�Őݒ�ύX����ꍇ

���Y�p�C�̐ݒ��ʂ��J��

	$ sudo raspi-config

[5 Interfacing Options] > [P6 Serial]��I��

��

Would you like a login shell to be accessible over serial? <- [No]��I��

��

Would you like the serial port hardware to be enabled? <- [Yes]��I��

��

<Finish>��I�����ă��Y�p�C���ċN������

	$ sudo reboot


### 01b. ���ڃR���t�B�O�t�@�C���̋L�q������������ꍇ

/boob/cmdline.txt�ɋL�q����Ă���```console=ttyAMA0,115200```���R�����g�A�E�g����B

��

���Y�p�C���ċN������

	$ sudo reboot


## 02. �ݒ�m�F

### serial0������������Ă��邱�Ƃ��m�F

	$ systemctl status serial-getty@ttyS0.service

> (�\����)  
> �� serial-getty@ttyS0.service - Serial Getty on ttyS0  
>   Loaded: loaded (/lib/systemd/system/serial-getty@.service; disabled; vendor preset:  
>   Active: inactive (dead)  
>     Docs: man:agetty(8)  
>           man:systemd-getty-generator(8)  
>           http://0pointer.de/blog/projects/serial-console.html  
> pi@raspberrypi:~ $  

������������Ă��Ȃ���Ή��L�R�}���h�Œ�~������

	$ sudo systemctl stop serial-getty@ttyAMA0.service
	$ sudo systemctl disable serial-getty@ttyAMA0.service

### 