# インストール

## パッケージ

以下のパッケージを使用します。

| パッケージ | 説明 |
|:---|:---|
| NDao.Generator | Dao を生成します。 |
| NDao | Dao を実行します。 |
| Ndao.Database.(database) | 各データベース用に NDao を設定します。|






## サービス登録

`AddDefaultDaos<T>()` または `AddDaos<T>(group)` を使ってサービスを登録します。

``` C#
WebApplicationBuilder builder = WebApplication.CreateBuilder(args);
builder.Services.AddDefaultDaos<MyConnector>();
```

起動時に Dao インスタンスを生成し、初回の処理を高速化する事ができます。

``` C#
WebApplication app = builder.Build();
app.Services.PreloadDefaultDaos<MyConnector>();
```

