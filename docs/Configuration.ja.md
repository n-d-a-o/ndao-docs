# 設定


## コネクター

コネクターの `OnConfiguring` メソッド内で `DaoConnectorSettings` を使用して様々な設定をします。

(TODO)


## サービス

### PreloadDaos, PreloadDefaultDaos
`PreloadDaos` と `PreloadDefaultDaos` は、Dao のインスタンス生成だけを行います。
Dao はシングルトンなので、事前に生成しておけば、初回使用時の処理を短縮できます。

```csharp
WebApplication app = builder.Build();
app.Services.PreloadDefaultDaos<MyConnector>();
```
