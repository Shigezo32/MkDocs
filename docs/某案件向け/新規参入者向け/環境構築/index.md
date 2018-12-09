# 環境構築トップ

## プロジェクトに関わるすべての人が対象

### Git

* [Git for Windows](https://gitforwindows.org/)
    * [インストール手順](./GitforWindowsのインストール)
* [TortoiseGit](https://tortoisegit.org/)
    * [インストール手順](./TortoiseGitのインストールと設定)
    * 必須ではないので好みで選んでいい
    * よく聞く Git クライアントだと [Sourcetree](https://ja.atlassian.com/software/sourcetree) や [GitHub Desktop](https://desktop.github.com/) など
* [WinMerge](http://winmerge.org/)
    * [インストール手順](./WinMergeのインストール)
    * 必須ではない、差分ビューアーを変更したい場合のみ

### Docker

* [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)
    * [インストール手順](./DockerforWindowsのインストールと設定)
* [Kitematic](https://kitematic.com/)
    * 必須ではない Image と Container の状態を GUI で見たいだけなら Visual Studio Code の Docker 拡張機能が便利

### Visual Studio Code

* [Visual Studio Code](https://code.visualstudio.com/download)
    * [インストール手順](./VisualStudioCodeのインストール)

#### 拡張機能

* [Japanese Language Pack for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=MS-CEINTL.vscode-language-pack-ja)
* [markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)
* [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [PlantUML](https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml)
* [Debugger for Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)
* [Angular v7 Snippets](https://marketplace.visualstudio.com/items?itemName=johnpapa.Angular2)
* [TSLint](https://marketplace.visualstudio.com/items?itemName=eg2.tslint)

!!! Todo
    プロジェクトの推奨拡張機能を設定することで周知を行うので設定次第削除する。

### その他

* [Java](https://www.java.com/ja/)
    * [インストール手順](./Javaのインストール)
    * UMLを書く場合は必須
* [Graphviz](http://www.graphviz.org/)
    * [インストール手順](./Graphvizのインストール)
    * UMLを書く場合は必須
* [Azure Storage Explorer](https://azure.microsoft.com/ja-jp/features/storage-explorer/)
    * 必須ではない、スタブデータを差し替える場合に利用する

## クライアント開発を行う人が対象

* [Node.js](https://nodejs.org/ja/)
    * [Node.js 10.14.1](https://nodejs.org/ja/download/releases/) を推奨バージョンとする
* [Angular CLI](https://cli.angular.io/)
    * [v7.1.0](https://github.com/angular/angular-cli/releases/tag/v7.1.0) を推奨バージョンとする
    * `npm install -g @angular/cli@7.1.0`
* [Google Chrome](https://www.google.co.jp/chrome/)
* [Fiddler](https://www.telerik.com/fiddler)

## サーバー開発を行う人が対象

* [Visual Studio 2017](https://visualstudio.microsoft.com/ja/downloads/)
    * 基本的にはエンタープライズ、無償試用版をインストールしライセンス認証を行う
* [SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)
    * 日本語のページだとバージョンが古いことが多いので、英語のページから `Japanese` をダウンロードする
* [SQL Server](https://www.microsoft.com/ja-jp/sql-server/sql-server-downloads)
    * 必須ではない、必要な場合は Visual Studio 2017 より先にインストールすること
* [dotnet-reportgenerator-globaltool](https://www.nuget.org/packages/dotnet-reportgenerator-globaltool/)
    * 4.0.4 を推奨バージョンとする
    * `dotnet tool install --global dotnet-reportgenerator-globaltool --version 4.0.4`
