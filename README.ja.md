# 始めに

## 例

## 特徴

NDao は以下のような特徴を持っています。

- SQL ファイルから C# のコードを生成し、Dao として使用する事ができます。
- SQL に記述するパラメーターやコードをコンパイル時にチェックする事ができます。
- データ操作 API を使って、データの取得と更新を行う事ができます。
- SQL のロギングができます。
- EF Core との併用が可能です。


# セットアップ

## パッケージ

以下のパッケージを使用します。

	NDao.Generator | コードを生成します。
	NDao | Dao を実行します。
	Ndao.Database.(database) | 各データベース用に NDao を設定します。

## サービス

`AddDefaultDaos<T>()` または `AddDaos<T>(group)` を使ってサービスを登録します。

``` C#
WebApplicationBuilder builder = WebApplication.CreateBuilder(args);
builder.Services.AddDefaultDaos<MyConnector>();
```

起動時に Dao インスタンスを生成し初回の処理を高速化する事ができます。

``` C#
WebApplication app = builder.Build();
app.Services.PreloadDefaultDaos<MyConnector>();
```


# データ操作

## 例

## ICrudDao

## Entity


# 動的 SQL

## 例

## Dao インターフェース

## Dao SQL

## Dto


# 設定

## Dao グループ

## Connector


# EF Core 併用



