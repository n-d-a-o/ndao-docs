# インストール


## NuGet パッケージ

現在、以下のパッケージを提供しています。

**NDao の NuGet パッケージ一覧**

| パッケージ名 | 説明 |
|:---|:---|
| NDao | Dao を実行します。(必須) |
| NDao.Generator | Dao を生成します。(必須) |
| Ndao.Database.Sqlite | SQLite 用に NDao を設定します。 |
| Ndao.Database.Postgres | PostgreSQL 用に NDao を設定します。 |

(他のデータベース用のパッケージも追加する予定ですが、時期は未定です。
また、設定をカスタマイズする事で自分で作成する事も一応可能です。)


## NuGet パッケージのインストール

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
	}
}
```


## サービス登録

`IServiceCollection` の拡張メソッド `AddDaos` または `AddDefaultDaos` を使ってサービスを登録します。


### AddDaos と AddDefaultDaos

`AddDaos` と `AddDefaultDaos` は、コネクターと Dao を生成し、コネクターと Dao グループを紐づけます。
Dao はどのグループに属しているかという情報を持っているので、コネクターと Dao が紐づきます。
`AddDaos` と `AddDefaultDaos` の違いは、紐づけるグループに名前があるかどうかだけです。


### 単一データベース

単一のデータベースに接続する場合は、以下のようにサービスを登録します。

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


### 複数データベース

複数のデータベースに接続する場合は、以下のようにサービスを登録します。

```csharp
// Program.cs

public class Program
{
	public static void Main(string[] args)
	{
		WebApplicationBuilder builder = WebApplication.CreateBuilder(args);
		builder.Services.AddDefaultDaos<Example1Connector>();
		builder.Services.AddDaos<Example2Connector>("Example2");
		...
	}
}
```

また、Dao には以下のようにグループ名を指定します。

```csharp
// Daos/Example2/IPersonDao.cs

// Example2Connector で接続する (Example2 Dao グループ)
[Dao("Example2")]
public interface IPersonDao
{
}
```

```csharp
// Daos/Example1/IPersonDao.cs

// Example1Connector で接続する (デフォルト Dao グループ)
[Dao]
public interface IPersonDao
{
}
```
