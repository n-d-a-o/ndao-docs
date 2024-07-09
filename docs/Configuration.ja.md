# 設定




## サービス

Dao の初回使用時の処理を高速化したい場合、`PreloadDaos` と `PreloadDefaultDaos` で Dao インスタンスを事前に生成しておく事ができます。

```csharp
WebApplication app = builder.Build();
app.Services.PreloadDefaultDaos<MyConnector>();
```
