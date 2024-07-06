# インストール

## NuGet パッケージのインストール

現在、以下のパッケージを提供しています。

| 名前                    | 説明                           |
|:-----------------------|:-------------------------------|
| NDao                   | Dao を実行します。(必須)           |
| NDao.Generator         | Dao を生成します。(必須)           |
| Ndao.Database.Sqlite   | SQLite 用に NDao を設定します。    |
| Ndao.Database.Postgres | PostgreSQL 用に NDao を設定します。|

必須のパッケージをインストールします。

```powershell
dotnet add package NDao
dotnet add package NDao.Generator
```

データベースの種類に応じて、必要なパッケージをインストールします。

```powershell
dotnet add package NDao.Database.Sqlite
```

## コネクター定義

`NDao.DaoConnector` を継承したクラスを作成します。
`OnConfiguring` メソッドをオーバーライドして、接続文字列を設定します。

```csharp
// ExampleConnector.cs

public class ExampleConnector : DaoConnector
{
	public override void OnConfiguring(DaoConnectorSettings settings)
	{
		settings.UseSqlite("Data Source=Example.db");
		settings.LogWriter = message => Console.WriteLine(message);
	}
}
```

## サービス登録

`IServiceCollection` の拡張メソッド `AddDaos` または `AddDefaultDaos` を使ってサービスを登録します。

```csharp
// Program.cs

public class Program
{
	public static void Main(string[] args)
	{
		WebApplicationBuilder builder = WebApplication.CreateBuilder(args);
		builder.Services.AddDefaultDaos<ExampleConnector>();
		...
	}
}
```


