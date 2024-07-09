# 設定

## サービス

起動時に Dao インスタンスを生成し、初回の処理を高速化する事ができます。

```csharp
WebApplication app = builder.Build();
app.Services.PreloadDefaultDaos<MyConnector>();
```
