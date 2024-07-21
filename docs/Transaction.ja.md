# トランザクション


## DaoTransaction

トランザクションを制御するには `NDao.DaoTransaction` を使用します。
`DaoTransaction` は、コミットやロールバックを行い、その際にログ出力を行います。
また、`DaoTransaction` を生成するために `NDao.DaoContext<TConnector>` を使用します。

***DaoTransaction プロパティ***
| プロパティ | 説明 |
|:---|:---|
| DbTransaction | `System.Data.Common.DbTransaction` オブジェクトを取得する。 |

***DaoTransaction メソッド***
| メソッド | 説明 |
|:---|:---|
| Commit | トランザクションをコミットする。 |
| CommitAsync | トランザクションをコミットする。(非同期) |
| Rollback | トランザクションをロールバックする。 |
| RollbackAsync | トランザクションをロールバックする。(非同期) |


### トランザクションの開始と完了

次の手順でトランザクションをコミットします。

1. `DaoContext` オブジェクトの `BeginTransaction` メソッドを呼び出して `DaoTransaction` オブジェクトを生成し、トランザクションを開始する。
2. Dao メソッドに `DaoTransaction` オブジェクトを渡す。
3. `DaoTransaction` オブジェクトの `Commit` メソッドを呼び出し、トランザクションを完了する。

下記のようにトランザクションを使用します。

```csharp
// Pages/Samples.cshtml.cs

public class SamplesModel : PageModel
{
    private readonly DaoContext<SampleConnector> context;

    ...

    public SamplesModel(DaoContext<SampleConnector> context, ...)
    {
        // DI で DaoContext インスタンスを受け取る
        this.context = context;
        ...
    }

    ...
}
```

```csharp
// トランザクションを開始する
using (DaoTransaction transaction = context.BeginTransaction())
{
    // Dao メソッドにトランザクションを渡す
    accountCrud.Insert(account, transaction);
    signupCrud.Update(signup, transaction);

    // トランザクションを完了する
    transaction.Commit();
}
```


## Dao

トランザクションに対応した Dao メソッドを定義するには、メソッドの末尾のパラメーターを `DaoTransaction` 型のパラメーターにします。

下記のように定義します。

```csharp
[Dao]
public interface ISampleDao
{
    // トランザクションは必須
    int UpdateX(string a, string b, DaoTransaction c);

    // トランザクションはオプション
    int UpdateY(string a, string b, DaoTransaction? c = null);
}
```
