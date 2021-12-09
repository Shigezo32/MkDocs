# Docker を利用して MagicOnion & .Net Core の開発環境を整える

## はじめに

Docker を利用して MagicOnion & .Net Core の開発環境を整えます。  

## 前提条件

* Visual Studio がインストールされていること
* Docker がインストールされていること

## サービス定義の作成

### サービス定義プロジェクトの作成

サービス定義プロジェクトを .NET Standard クラスライブラリ で作成します。  

### NuGet パッケージのインストール (サービス定義)

`MagicOnion` をインストールします。  

```sh
Install-Package MagicOnion
```

### インターフェースの作成

```C#
public interface IMyFirstService : IService<IMyFirstService>
{
    UnaryResult<int> SumAsync(int x, int y);
}
```

[commit:サービス定義の作成](https://github.com/Shigezo32/MagicOnionInDocker/commit/6827727974abf3c58fd3749fccf1a1bc2d03fcc0)  

## サーバーの作成

### サーバープロジェクトの作成

サーバープロジェクトを .NET Core コンソールアプリ で作成します。  

### NuGet パッケージのインストール (サーバー)

`MagicOnion` をインストールします。  

```sh
Install-Package MagicOnion
```

### 参照の追加 (サーバー)

サービス定義プロジェクト を参照に追加します。  

```xml
<ItemGroup>
  <ProjectReference Include="..\MagicOnionInDocker.ServiceDefinition\MagicOnionInDocker.ServiceDefinition.csproj" />
</ItemGroup>
```

### サービスの作成

```C#
public class MyFirstService : ServiceBase<IMyFirstService>, IMyFirstService
{
    public async UnaryResult<int> SumAsync(int x, int y)
    {
        Logger.Debug($"Received:{x}, {y}");

        return x + y;
    }
}
```

### サーバーの起動

```C#
class Program
{
    static void Main(string[] args)
    {
        GrpcEnvironment.SetLogger(new Grpc.Core.Logging.ConsoleLogger());

        var service = MagicOnionEngine.BuildServerServiceDefinition(isReturnExceptionStackTraceInErrorDetail: true);

        var server = new global::Grpc.Core.Server
        {
            Services = { service },
            Ports = { new ServerPort("localhost", 12345, ServerCredentials.Insecure) }
        };

        // launch gRPC Server.
        server.Start();

        Console.ReadLine();
    }
}
```

[commit:サーバーの作成](https://github.com/Shigezo32/MagicOnionInDocker/commit/9c4d4d1b0c8dfe036f3930d44f1fc1876e3c05b0)  

## クライアントの作成

今回は単純なクライアントではなく、ASP.NET Core を利用した Swagger をクライアントとして利用します。

### クライアントプロジェクトの作成

クライアントプロジェクトを ASP.NET Core Web アプリケーション で作成します。  

### NuGet パッケージのインストール (クライアント)

`MagicOnion` と `MagicOnion.HttpGateway` をインストールします。  

```sh
Install-Package MagicOnion
Install-Package MagicOnion.HttpGateway
```

### 参照の追加 (クライアント)

サービス定義プロジェクト と サーバープロジェクト を参照に追加します。  

```xml
<ItemGroup>
  <ProjectReference Include="..\MagicOnionInDocker.Server\MagicOnionInDocker.Server.csproj" />
  <ProjectReference Include="..\MagicOnionInDocker.ServiceDefinition\MagicOnionInDocker.ServiceDefinition.csproj" />
</ItemGroup>
```

### クライアントの起動

```C#
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        var service = MagicOnionEngine.BuildServerServiceDefinition(new Assembly[] { Assembly.GetAssembly(typeof(MyFirstService)) }, new MagicOnionOptions(true)
        {
            MagicOnionLogger = new MagicOnionLogToGrpcLogger()
        });
        // Add MagicOnionServiceDefinition for reference from Startup.
        services.Add(new ServiceDescriptor(typeof(MagicOnionServiceDefinition), service));
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        var magicOnion = app.ApplicationServices.GetService<MagicOnionServiceDefinition>();
        var xmlPath = Path.Combine(AppContext.BaseDirectory, "MagicOnionInDocker.ServiceDefinition.xml");
        app.UseMagicOnionSwagger(magicOnion.MethodHandlers, new MagicOnion.HttpGateway.Swagger.SwaggerOptions("MagicOnionInDocker.Server", "Swagger Integration Test", "/")
        {
            XmlDocumentPath = xmlPath
        });
        app.UseMagicOnionHttpGateway(magicOnion.MethodHandlers, new Channel("localhost:12345", ChannelCredentials.Insecure));
    }
}
```

ここまでで一通り動作するので、マルチスタートアッププロジェクトでサーバーとクライアントを実行し動作確認しておくといいです。  

[commit:クライアントの作成](https://github.com/Shigezo32/MagicOnionInDocker/commit/257f93be6304238014cb9b4c8b0a3411acc9360a)  

## コンテナー オーケストレーター サポートの追加

今回は Docker Compose を利用します。  
サーバープロジェクト と クライアントプロジェクト で Visual Studio でプロジェクトを右クリックから `コンテナー オーケストレーター サポート` を追加します。  
`docker-compose.yml` と `Dockerfile` は自動生成されたものから変更する必要はありません。  

[commit:コンテナー オーケストレーター サポートの追加](https://github.com/Shigezo32/MagicOnionInDocker/commit/b5a0f56a4b8c43b0e38454b2775065934da3758a)  

## host の変更

Docker で動作するように host を変更します。  

### サーバープロジェクト

```C#
var server = new global::Grpc.Core.Server
{
    Services = { service },
    Ports = { new ServerPort("0.0.0.0", 12345, ServerCredentials.Insecure) }
};
```

### クライアントプロジェクト

```C#
app.UseMagicOnionHttpGateway(magicOnion.MethodHandlers, new Channel("magiconionindocker.server", 12345, ChannelCredentials.Insecure));
```

[commit:host の変更](https://github.com/Shigezo32/MagicOnionInDocker/commit/bbca46ca70f4f3942d3f9b8614682d52c09e7e73)  

## TODO

* host / port の設定ファイル化