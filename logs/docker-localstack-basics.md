---
title: DockerとLocalStackの基本整理
tags:
  - Docker
  - LocalStack
  - AWS
private: true
updated_at: ""
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

# DockerとLocalStackの基本整理

Dockerは、アプリケーションと実行に必要なライブラリや設定をコンテナとしてまとめ、環境差分を減らすための技術です。開発環境、本番環境、CIで同じイメージを使うことで「自分のPCでは動いた」を減らせます。

## 基本用語

| 用語 | 意味 |
|---|---|
| Dockerfile | イメージを作るためのレシピ |
| イメージ | アプリケーションと実行環境を固めたテンプレート |
| コンテナ | イメージから起動した実行中の環境 |
| Docker Hub | Dockerイメージを配布・取得できるレジストリ |
| Docker Compose | 複数コンテナをまとめて定義・起動する仕組み |

## 開発環境と本番環境

開発者はDockerfileを用意し、アプリケーションを動かすためのイメージを作ります。本番環境では、そのイメージをコンテナとして起動します。

依存関係のインストールは、通常イメージ作成時に済ませます。本番起動時に毎回 `npm install` を実行する構成は、起動時間や再現性の面で不利になりやすいです。

## Docker Desktop

Docker Desktopは、MacやWindowsでDockerを使うための開発ツールです。ローカルでコンテナを動かし、本番に近い環境を手元で再現できます。

Docker Desktop以外にも、Rancher Desktopなどの選択肢があります。プロジェクトの制約、ライセンス、Kubernetesの利用有無に合わせて選びます。

## Docker Compose

Webアプリ、DB、Redis、LocalStackなど、複数のコンテナをまとめて動かしたい場合はDocker Composeが便利です。

```yaml
services:
  app:
    build: .
    ports:
      - "3000:3000"
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: password
```

## LocalStack

LocalStackは、S3やDynamoDBなどのAWSサービスをローカルで模倣するためのツールです。Docker上でLocalStackを起動すると、AWS本番環境に接続せずに開発やテストを進められます。

S3互換APIをローカルで確認する場合、エンドポイントは一般的に `http://localhost:4566` を使います。ただし、実際のバケット名やオブジェクトキーにはプロジェクト固有情報が含まれやすいため、公開記事ではサンプル値に置き換えます。

```txt
http://localhost:4566/<bucket-name>/<object-key>
```

## Kubernetesとの違い

Docker Composeは、主にローカル開発や小規模な複数コンテナ管理で使います。Kubernetesは、本番運用でのスケーリング、自動復旧、ローリングアップデートなどを担うコンテナオーケストレーション基盤です。

最初の学習では、Dockerfile、イメージ、コンテナ、Composeの関係を押さえてからKubernetesに進むと理解しやすくなります。

## 関連リンク

- https://www.docker.com/ja-jp/
