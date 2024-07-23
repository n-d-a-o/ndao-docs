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

`IServiceCollection` の拡張メソッド `AddDaos(string group)` または `AddDefaultDaos()` を使って必要なサービスを登録します。


### 単一のデータベースの場合

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


### 複数のデータベースの場合

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

また、Dao に対してグループを設定します。
下記のように、`Dao` 属性でグループ名を指定します。

```csharp
// Daos/Sample1/ISample1PersonDao.cs

[Dao]
public interface ISample1PersonDao
{
}

// Dao グループ名 = なし (Sample1Connector で接続する)
```

```csharp
// Daos/Sample2/ISample2PersonDao.cs

[Dao("Sample2")]
public interface ISample2PersonDao
{
}

// Dao グループ名 = "Sample2" (Sample2Connector で接続する)
```


### AddDaos と AddDefaultDaos

`Dao` 属性は Dao と Dao グループを紐づけます。
`AddDaos` と `AddDefaultDaos` は、Dao グループとコネクターを紐づけます。
そのため、Dao はコネクターと紐づいて、接続情報を得る事ができます。

`AddDaos` と `AddDefaultDaos` には、グループに名前を付けるかどうかの違いしかありません。
`Dao` 属性でグループ名を指定しないグループ (デフォルトグループ) に対しては `AddDefaultDaos` を使い、その他のグループに対しては `AddDaos` を使います。
