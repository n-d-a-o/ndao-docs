# トランザクション


### DaoTransaction

トランザクションを制御するには `NDao.DaoTransaction` と `NDao.DaoContext<TConnector>` を使用します。
`DaoTransaction` は、コミットやロールバックを行い、その際にログ出力を行います。
`DaoContext` は `DaoTransaction` を生成します。

以下は RazorPages での例です。

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

以下の手順でトランザクションを使用します。

* `DaoContext` オブジェクトの `BeginTransaction` メソッドを呼び出し、 `DaoTransaction` オブジェクトを生成する
* `DaoTransaction` オブジェクトを生成する事で、トランザクションを開始する
* Dao メソッドに `DaoTransaction` オブジェクトを渡す
* `DaoTransaction` オブジェクトの `Commit` メソッドを呼び出し、トランザクションを完了する

`DaoTransaction` は、以下のメンバーを持っています。

**DaoTransaction プロパティ一覧**

| プロパティ | 説明 |
|:---|:---|
| DbTransaction | `System.Data.Common.DbTransaction` オブジェクトを取得する。 |

**DaoTransaction メソッド一覧**

| メソッド | 説明 |
|:---|:---|
| Commit | トランザクションをコミットする。 |
| CommitAsync | トランザクションをコミットする。(非同期) |
| Rollback | トランザクションをロールバックする。 |
| RollbackAsync | トランザクションをロールバックする。(非同期) |


### Dao

トランザクションを使用するには、Dao メソッドの末尾のパラメーターを `DaoTransaction` にします。

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

