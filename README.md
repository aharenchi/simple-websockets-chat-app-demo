# docker + aws-cli + websockets-chat-app-demo

## 環境構築
### 自身のIAMの設定を環境設定する
```
$ export AWS_ACCESS_KEY_ID='xxxxxxxxxxxxx'
$ export AWS_SECRET_ACCESS_KEY='xxxxxxxxxxxxxxxxxx'
```

### aws-cliコンテナ起動＆接続
```
$ docker-compose build
$ docker-compose run --rm aws-cli bash
$ aws --version
$ cd /usr/src/app/simple-websockets-chat-app
```

### サンプルプロジェクトをデプロイ
```
$ sam deploy --guided

	Setting default arguments for 'sam deploy'
	=========================================
	Stack Name [sam-app]: websockets-chat-app-demo-stack          
	AWS Region [us-east-1]: 
	Parameter TableName [simplechat_connections]: 
	#Shows you resources changes to be deployed and require a 'Y' to initiate deploy
	Confirm changes before deploy [y/N]: 
	#SAM needs permission to be able to create roles to connect to the resources in your template
	Allow SAM CLI IAM role creation [Y/n]: 
	Save arguments to samconfig.toml [Y/n]: 

	Looking for resources needed for deployment: Not found.
	Creating the required resources...
	Successfully created!

		Managed S3 bucket: aws-sam-cli-managed-default-samclisourcebucket-1qa0gqr3oz6qn
		A different default S3 bucket can be set in samconfig.toml

	Saved arguments to config file
	Running 'sam deploy' for future deployments will use the parameters saved above.
	The above parameters can be changed by modifying samconfig.toml
	Learn more about samconfig.toml syntax at 
	https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-config.html

	Deploying with following values
	===============================
	Stack name                 : websockets-chat-app-demo-stack
	Region                     : us-east-1
	Confirm changeset          : False
	Deployment s3 bucket       : aws-sam-cli-managed-default-samclisourcebucket-1qa0gqr3oz6qn
	Capabilities               : ["CAPABILITY_IAM"]

以下省略
```
CloudFormationによって、以下のようにAPIがdeployされている

```
Key                 WebSocketURI
Description         The WSS Protocol URI to connect to
Value               wss://{YOUR-API-ID}.execute-api.{YOUR-REGION}.amazonaws.com/Prod  
```

## 実行方法

```
# wscatコマンドのインストール
$ npm install -g wscat
# APIGateayのステージエンドポイントにwssコネクションを貼る
$ wscat -c wss://{YOUR-API-ID}.execute-api.{YOUR-REGION}.amazonaws.com/prod
connected (press CTRL+C to quit)

# メッセージを送信
> {"action":"sendmessage", "data":"hello world"}
< hello world
```
### 注意点
+ 3つLambdaのruntimeはNode.js10で実行することを推奨します。Node.js12で動作確認できませんでした。

## 参考文献
+ [APIGatewayでWebSocketが利用可能になったのでチャットAPIを構築してみた](https://qiita.com/G-awa/items/472bc1a9d46178f3d7a4)
