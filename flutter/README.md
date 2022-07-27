# DockerでFlutter環境構築する手順

## 00. 事前確認
```bash
vi .env
```

## 01. ビルド
```bash
docker-compose build
```

## 02. コンテナを構築、起動
```bash
docker-compose up -d
```

## 03. 起動したコンテナに入る
```bash
docker exec -it flutter bash
```
## 04. flutterアプリを作成
```bash
cd ${APP_CODE_PATH_CONTAINER}
flutter create .
```
## 05. サーバーを起動
```bash
flutter run -d web-server --web-port=${WEB_SERVER_PORT} --web-hostname 0.0.0.0
```