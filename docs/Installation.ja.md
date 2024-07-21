# インストール


## NuGet パッケージ

NDao には下記のパッケージがあります。

***NDao の NuGet パッケージ***
| パッケージ名 | 説明 |
|:---|:---|
| NDao | Dao を実行する。(必須) |
| NDao.Generator | Dao を生成する。(必須) |
| Ndao.Database.Sqlite | SQLite 用に NDao を設定する。 |
| Ndao.Database.Postgres | PostgreSQL 用に NDao を設定する。 |

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
// SampleConnector.cs

public class SampleConnector : DaoConnector
{
    public override void OnConfiguring(DaoConnectorSettings settings)
    {
        settings.UseSqlite("Data Source=Sample.db");
    }
}
```


## サービス登録

`IServiceCollection` の拡張メソッド `AddDaos` または `AddDefaultDaos` を使ってサービスを登録します。


### AddDaos と AddDefaultDaos

`AddDaos` と `AddDefaultDaos` は、コネクターと Dao グループを紐づけます。
Dao は属するグループの情報を持っているので、コネクターと Dao が紐づきます。
`AddDaos` と `AddDefaultDaos` の違いは、紐づけるグループに名前があるかどうかだけです。


### 単一データベース

単一のデータベースに接続する場合は、下記のようにサービスを登録します。

```csharp
// Program.cs

public class Program
{
    public static void Main(string[] args)
    {
        WebApplicationBuilder builder = WebApplication.CreateBuilder(args);
        builder.Services.AddDefaultDaos<SampleConnector>();
        ...
    }
}
```


### 複数データベース

複数のデータベースに接続する場合は、下記のようにサービスを登録します。

```csharp
// Program.cs

public class Program
{
    public static void Main(string[] args)
    {
        WebApplicationBuilder builder = WebApplication.CreateBuilder(args);
        builder.Services.AddDefaultDaos<Sample1Connector>();
        builder.Services.AddDaos<Sample2Connector>("Sample2");
        ...
    }
}
```

また、Dao には下記のようにグループ名を指定します。

```csharp
// Daos/Sample1/IPersonDao.cs

// デフォルト Dao グループ = Sample1Connector で接続する
[Dao]
public interface IPersonDao
{
}
```

```csharp
// Daos/Sample2/IPersonDao.cs

// Sample2 Dao グループ = Sample2Connector で接続する
[Dao("Sample2")]
public interface IPersonDao
{
}
```
