#### 2020/05/17 作成

# EC2(Amazon Linux 2)とs3の連携



## 01. AWS CLIの設定

AWS認証情報を確認する。

	$ aws configure list

(表示例)

> Name	Value	Type	Location  
> \----	-----	----	--------  
> profile	<not set>	None	None  
> access_key	****************????	shared-credentials-file  
> secret_key	****************????	shared-credentials-file  
>     region	ap-northeast-1	config-file	~/.aws/config  

認証情報が入っていない場合は下記コマンドで認証情報を追加していく。

	$ aws configure

(表示例)

> AWS Access Key ID [None]:  
> AWS Secret Access Key [None]:  
> Default region name [None]:  
> Default output format [None]:  

AWS Access Key IDとAWS Secret Access Keyは、下記手順から確認する。

コンソールにログインして[IAM] > [ダッシュボード] > [セキュリティ認証情報の管理]を選択。

↓

[アクセスキー] > [新しいアクセスキーの作成]を選択し、アクセスキーをダウンロードする。
※アクセスキーは無くさないよう必ず控えておくこと

↓

Default region nameに"ap-northeast-1"を入力。

※使用しているec2インスタンスのリージョンが"us-east-1"ならそれに合わせても平気かもしれない(未実施)。

↓

Default output formatに"text"を入力。

↓

再度、`$ aws configure list`を叩いて、入力情報に漏れがないことを確認。

## 02. s3 bucketの作成

bucketを作成(AWS CLIの場合)

	$ aws s3 mb s3://<バケット名>

(表示例)

> make_bucket: s3://<バケット名>

	$ aws s3 ls

(表示例)

> 2020-05-17 15:34:10 <バケット名>


## 03. エンドポイントの作成

[VPC] > [エンドポイント] > [ポリシー] > [ポリシーの編集]を選択。

↓

[カスタム]にチェックを入れて[ポリシー作成ツール]を選択。

↓

[Select Type of Policy]は"S3 Bucket Policy"を選択。

[Effect]は"Allow"にチェックし、[Principal]は"*"を入力。

[AWS Service]に"Amazon S3"、[Actions]に"s3:getObject"と"s3:putObject"を選択。

[ARN]に下記を追加。

"aws:s3:::amazonlinux.*.amazonaws.com/*,
aws:s3:::<s3バケット名>/,
aws:s3:::<s3バケット名>/*"

↓

[Generate Policy]をクリックしてポリシーを自動作成。

↓

自動作成したものをポリシーにコピペ。

## 04. s3にファイルをアップロード

	$ touch hoge.txt
	$ aws s3 cp hoge.txt s3://<バケット名>/hoge.txt
	$ aws s3 ls s3://<バケット名>

(表示例)

> 2020-05-17 15:39:15   37462196 hoge.txt

s3へのアップロード完了。

## 付録. s3でアップロードしたファイルをブラウザからアクセスする

[VPC] > [エンドポイント] > [ポリシー]の作成で、"getObjectAcl"を追加。

↓

パブリックからアクセスできるよう下記コマンドでs3にファイルをアップロード。

	$ aws s3 cp --acl public-read <ファイル名> s3://<バケット名>/


下記URLにブラウザでアクセスできるようになる。

http://<バケット名>.s3.amazonaws.com/<ファイル名>