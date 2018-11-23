# Docker を利用して Angular & ASP.NET Core の開発環境を整える

## はじめに

Microsoft が提供している [ASP.NET Core で Angular プロジェクト テンプレートを使用する](https://docs.microsoft.com/ja-jp/aspnet/core/client-side/spa/angular?view=aspnetcore-2.1&tabs=visual-studio) を Docker で動作させるための手順です。  

## 前提条件

* Visual Studio がインストールされていること
* Node.js がインストールされていること
* Docker がインストールされていること

## プロジェクトの作成

コンソールで新しくプロジェクトを作ってもいいんですが、Docker サポートを追加する際にソリューションファイルを作ることになるので、Visual Studio から作成します。  

[commit:プロジェクトの作成](https://github.com/Shigezo32/AngularWithAspNetCore/commit/4576adaa1e31f2864b1a851e4c45755c21c9f40a)  

## Angular のバージョンアップ

2018/11/23 現在　ASP.NET Core の Angular テンプレートはバージョンが古いです。  
折角なので新しいバージョンが利用したいので最新化します。  

テンプレートに興味はないので別途作成した Angular プロジェクトで上書きするのが手っ取り早いです。  
テンプレートをそのまま利用したい場合は [Angular Update Guide](https://update.angular.io/) を参考にしてください。  

[commit:Angular プロジェクトを新規作成したものに差し替え](https://github.com/Shigezo32/AngularWithAspNetCore/commit/e2a3c06261ce378e1f5afb7857ff51233accac89)  

`ng new ClientApp --style=scss` でプロジェクトを作成しましたが若干古いようなので最新化します。  

```sh
ng update @angular/core
ng update @angular-devkit/build-angular
ng update @types/node
ng update @types/jasmine @types/jasminewd2 jasmine-core jasmine-spec-reporter
ng update codelyzer
ng update karma karma-chrome-launcher karma-coverage-istanbul-reporter karma-jasmine karma-jasmine-html-reporter
ng update protractor
ng update ts-node
ng update tslint
```

[commit:各種パッケージの更新](https://github.com/Shigezo32/AngularWithAspNetCore/commit/495d5c8a44ba2d11db6426269a54d125242d1169)  

別途作成したプロジェクトで上書きした場合、ビルド時の出力先が異なるので修正します。  
`"outputPath": "dist/ClientApp",` ⇒ `"outputPath": "dist",`  

[commit:ビルド時の出力先を変更](https://github.com/Shigezo32/AngularWithAspNetCore/commit/17621198f0d98b3c6ae9a73958b157104329865e)  

## Docker サポートの追加

Visual Studio でプロジェクトを右クリックから `Docker サポート` を追加します。  

[commit:Docker サポートの追加](https://github.com/Shigezo32/AngularWithAspNetCore/commit/3fd3ee61f7411498a04152405cd084ad6e282c05)  

このままではビルドできないので `.dockerignore` で `node_modules` を除外するように設定し、`Dockerfile` で Node.js をインストールするようにします。  

```.dockerignore
.dockerignore
.env
.git
.gitignore
.vs
.vscode
*/bin
*/obj
**/.toolstarget
*/*/node_modules
```

```Dockerfile
FROM microsoft/dotnet:2.1-aspnetcore-runtime AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM microsoft/dotnet:2.1-sdk AS build
WORKDIR /src
COPY ["AngularWithAspNetCore/AngularWithAspNetCore.csproj", "AngularWithAspNetCore/"]
RUN dotnet restore "AngularWithAspNetCore/AngularWithAspNetCore.csproj"
COPY . .
WORKDIR "/src/AngularWithAspNetCore"
RUN dotnet build "AngularWithAspNetCore.csproj" -c Release -o /app

RUN apt-get -qq update && apt-get install build-essential -y && apt-get install -my wget gnupg && apt-get -qq -y install bzip2
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash -
RUN apt-get install -y nodejs

FROM build AS publish
RUN dotnet publish "AngularWithAspNetCore.csproj" -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AngularWithAspNetCore.dll"]
```

[commit:Docker を利用したビルドが行えるように](https://github.com/Shigezo32/AngularWithAspNetCore/commit/65bd4a9d6326226603d3301bb10a3996643f055d)  

この設定では Visual Studio からのデバッグ実行で Angular を起動&実行は出来ないので、コンソールからビルドと実行を行い動作することを確認します。

```sh
docker build -t "angularwithaspnetcore" -f "Dockerfile" ".."
```

```sh
docker run --rm -it -p 8080:80 angularwithaspnetcore
```

## おわりに

この設定では Visual Studio からのデバッグ実行で Angular を起動&実行は出来ません。  
ASP.NET Core のデバッグ実行時に Angular を起動することに意味はないので、Angular 単体で実行できるようにしておくといいです。  
Docker を利用して Angular を実行する場合は [こちら](../Dockerを利用してAngularの開発環境を整える) の記事を参考にしてください。  

[commit:Angular プロジェクトで Docker を利用したビルドが行えるように](https://github.com/Shigezo32/AngularWithAspNetCore/commit/577249450d28d3fb6ebdf9d35c65cf33049f1208)  

## TODO

* サーバーサイドレンダリング