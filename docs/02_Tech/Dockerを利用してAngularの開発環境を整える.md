# Dockerを利用してAngularの開発環境を整える

## はじめに

Angularの開発環境はローカルで整えた方が開発しやすいんですが、複数のプロジェクトを同時にこなすような場合に Node.js や Angular CLI のバージョン違いでうまく動かなかったりしたので Docker を利用して Angular の開発環境を整えます。

## 前提条件

* Docker がインストールされていること

## Node.js & Angular の Docker Image の作成

Angular の公式イメージがあるわけではなさそうなので自分で作成します。  
バージョンは適宜変更してください。  
作成が手間であれば[こちら](https://hub.docker.com/r/shigezo/angular/)を利用してください。  

Dockerfile

```Dockerfile
# Node.js のバージョンを変更する場合は以下のURLからタグを確認してください
# https://hub.docker.com/r/library/node/tags/
FROM node:10.13-stretch

# Angular CLI のバージョンを変更する場合は以下のURLからタグを確認してください
# https://www.npmjs.com/package/@angular/cli?activeTab=versions
RUN npm install -g @angular/cli@7.0.6

WORKDIR /app

# EXPOSE 4200
```

Dockerfile をビルドします。

```sh
docker build -t angular:7.0.6 .
```

基本的にはこれだけでコマンドが実行できるようになります。  

```sh
docker run --rm -it angular:7.0.6 npm -v
docker run --rm -it -v ${PWD}:/app angular:7.0.6 npm install
docker run --rm -it angular:7.0.6 ng v
docker run --rm -it -v ${PWD}:/app angular:7.0.6 ng g component my-new-component
```

## 実行 & ビルド 環境の設定

Windows 環境で Linux のコンテナーを実行しており、node-sassを利用している場合はビルドに失敗します。  
node_volumes ディレクトリをマウントしないよう設定すれば回避できるのでそのための設定を用意します。  

.dockerignore  

```.dockerignore
.vs
.git
dist
node_modules

.dockerignore
.gitignore
```

Dockerfile

```Dockerfile
# Dockerfile
RUN mkdir /app
WORKDIR /app
COPY . /app
RUN yarn --pure-lockfile
```

docker-compose.build.yml

```yml
version: "3"
services:
  client:
    image: angular:7.0.6
    build: .
    command: bash -c "npm install && ng build --prod --aot"
    volumes:
      - .:/app
      - /app/node_modules/
    ports:
      - "4200:4200"
```

docker-compose.serve.yml

```yml
version: "3"
services:
  client:
    image: angular:7.0.6
    build: .
    command: bash -c "npm install && ng serve --host 0.0.0.0"
    volumes:
      - .:/app
      - /app/node_modules/
    ports:
      - "4200:4200"
```

### サーバー実行

ローカルでの実行と異なり、実行中にファイルを変更しても即時反映されないので使い勝手が悪いです。  

```sh
docker-compose -f docker-compose.serve.yml up
```

### ビルド実行

```sh
docker-compose -f docker-compose.build.yml up
```

## サンプル

* [リポジトリ(GitHub)](https://github.com/Shigezo32/AngularInDocker)
* [Docker Hub](https://hub.docker.com/r/shigezo/angular/)

## 参考

* [Dockerでnode-SASSを使うとNode Sass could not find a binding for your current environment: Linux 64-bit with Node.js X.X...](https://budougumi0617.github.io/2018/04/04/fail-node-sass-on-docker/)
* [Docker ALPINE Linux throws node-sass missing binding error](https://github.com/sass/node-sass/issues/2165#issuecomment-347043659)
