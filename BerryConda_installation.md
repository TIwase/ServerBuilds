# ラズパイにconda環境を構築する方法&使い方

anacondaは導入できないため、raspberryPi用のBerry Condaをインストールする

## 01. Berry Condaのインストール

Berry Condaのgithubページからインストーラをダウンロードする

	## raspberryPi2, 3, 4の場合 ##
	python3系インストーラ
	$ wget https://github.com/jjhelmus/berryconda/releases/download/v2.0.0/Berryconda3-2.0.0-Linux-armv7l.sh

	python2系インストーラ
	$ wget https://github.com/jjhelmus/berryconda/releases/download/v2.0.0/Berryconda2-2.0.0-Linux-armv7l.sh
	
	## raspberryPi1, zeroの場合 ##
	python3系インストーラ
	$ wget https://github.com/jjhelmus/berryconda/releases/download/v2.0.0/Berryconda3-2.0.0-Linux-armv6l.sh

	python2系インストーラ
	$ wget https://github.com/jjhelmus/berryconda/releases/download/v2.0.0/Berryconda2-2.0.0-Linux-armv6l.sh

Berry Condaをインストール

	$ chmod +x Berryconda3-2.0.0-Linux-armv7l.sh
	$ ./Berryconda3-2.0.0-Linux-armv7l.sh
	
	インストールしたcondaにパスが通っているか確認する
	$ echo $PATH

一度、terminalを閉じて再接続して`$ conda info -e`や`$ conda list`等でcondaコマンドが使えることを確認できればok

## 02. python仮想環境の作成

	$ conda create -n <仮想環境名> python=3.6.1

作成した仮想環境の確認

	$ conda info -e

(表示例)

> pytest               /home/user/berryconda3/envs/pytest  
> root              *  /home/user/berryconda3

作成した仮想環境を起動

	$ source activate pytest

(表示例)

> (pytest) pi@raspi4:~ $  

**※root環境からpytestに切り替わってることを確認**

インストールされているパッケージ一覧を表示する

	$ conda list

パッケージのインストール

	$ conda install <パッケージ名>
Active状態の仮想環境を終了する

	$ source deactive <仮想環境名>
	