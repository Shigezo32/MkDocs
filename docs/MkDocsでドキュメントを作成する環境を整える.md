# MkDocsでドキュメントを作成する環境を整える

## はじめに

ある程度の規模のプロジェクトになるときちんとしたドキュメントが欲しくなります。  
誰かひとりがすべての仕様を把握、コードレビューまで出来るようなスーパーマンがおり、ドキュメントが納品物に含まれない場合は必要ないかもしれませんが、割とレアケースなんじゃないでしょうか。  

ドキュメントのフォーマットとしてよくあるのがExcelやWordですが、表現力が高く使えない人が少ないのはいいのですが、頻繁に修正が発生するようなプロジェクトの場合、修正差分の比較がしんどいです。  
SVNやGitでバージョン管理している場合はまだマシですが、SharePointやファイルサーバーで管理しているようなプロジェクトではもう大変です、ハゲます。  

ドキュメントは生ものです、最初にきちんとしたものを作ったとしても、継続してメンテナンスされなければ意味がありません。  
人は怠惰です、ドキュメントが大切であることは理解していても手間がかかるようでは積極的にメンテナンスしようとは思いません。  
それが仕事であれば嫌々ながらメンテナンスするでしょうが、嫌々やる仕事は生産性が低くなりますし成果物の品質も悪くなります。  

というわけで、可能な限り楽をしつつそれっぽいドキュメントを作るための手段としてMkDocsを利用しましょう。  

## MkDocsってなんぞ？

markdown形式のファイルをhtmlに変換することができる静的サイトジェネレータです。  
筆者は怠惰なのでmarkdown以外を覚える気はありません、markdownはGitHub(クローン含む)のIssueやWikiでも利用するので覚えておくと便利なのです。  
小規模～中規模ぐらいのプロジェクトの場合、GitHubのWikiでドキュメントを書いてもいいんですが、複数のリポジトリを跨いだ内容の記載がしたかったり、見た目をちょっと弄ったりしたい場合は大変です。  
専用のリポジトリ作ってGithub Pages使え？ (∩ﾟдﾟ)ｱｰｱｰきこえなーい  

MkDocsはマテリアルデザインのテーマが優秀ですし、CSSもカスタマイズできるのでやる気になれば好き放題できそうです。  
また、最悪markdownを拡張することで足りない表現力を補うこともできます。  

## 実行環境構築

Dockerのイメージが配布されているのでそれを利用しましょう。  
大した手間ではないのと、拡張などで必要なパッケージが増えることもあるので自分でイメージを作成した方がいいんですが、今回は時間がないので今回は[squidfunk/mkdocs-material](https://hub.docker.com/r/squidfunk/mkdocs-material/)を使ってみることにします。  

### プロジェクトの作成

```sh
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material new ${プロジェクト名}
```

### ビルド

```sh
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material build
```

### サーバー実行

```sh
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs squidfunk/mkdocs-material
```

## 編集環境構築

markdownは単なるテキストなので、メモ帳でもなんでも好きなエディタで編集することができます。  
サーバー実行中にファイルを更新すると即時にビルドされブラウザで表示している場合は自動でリロードされます。  
正直なところ単純に更新するだけであれば何のエディタを利用してもいいんですが、作業の効率化とドキュメントの品質を向上させるために [Visual Studio Code](https://code.visualstudio.com/) をおススメします。  

### 拡張機能の追加

#### [Debugger for Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)

Chromeでデバッグするための拡張ですが、F5でChromeの起動と同時にサーバーを実行するようにします。  
`launch.json`でデバッグ実行時にChromeを起動するように設定し、`tasks.json`でデバッグ実行の開始/終了時にサーバーを実行/終了するコマンドを定義します。  

```launch.json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome",
            "url": "http://localhost:8000",
            "webRoot": "${workspaceFolder}",
            "preLaunchTask": "serve",
            "postDebugTask": "stop"
        }
    ]
}
```

```tasks.json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "serve",
            "type": "shell",
            "isBackground": true,
            "command": "docker run --rm -it -p 8000:8000 -v ${PWD}:/docs --name=mkdocs squidfunk/mkdocs-material",
            "problemMatcher": {
                "owner": "custom",
                "pattern": {
                    "regexp": "^$"
                },
                "background": {
                    "activeOnStart": true,
                    "beginsPattern": ".*Building documentation.*",
                    "endsPattern": ".*Start detecting changes.*"
                }
            }
        },
        {
            "label": "stop",
            "type": "shell",
            "command": "docker stop mkdocs"
        }
    ]
}
```

#### [markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)

markdown向けの静的解析ツールで、設定したルールに沿わないものは即時に警告が表示されます。  
表示された警告を解消することで誰が編集してもドキュメントの体裁を統一することができるようになります。  

## 参考

* [MkDocsによるドキュメント作成](https://qiita.com/mebiusbox2/items/a61d42878266af969e3c)
* [Angular + VSCode でデバッグ開始したときに ng serve する](https://qiita.com/amay077/items/62e1eb656fbd730b3dd1)

## TODO

* markdownlintのルール設定
* textlintの利用とルール設定
* PlantUMLを利用した図の作成