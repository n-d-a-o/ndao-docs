# トランザクション


### DaoContext

トランザクションを制御するには `DaoContext<TConnector>` と `DaoTransaction` を使用します。

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
// DaoContext を使って、トランザクションを開始する
using (DaoTransaction transaction = context.BeginTransaction())
{
	// Dao メソッドに DaoTransaction を渡す
	accountCrud.Insert(account, transaction);
	signupCrud.Update(signup, transaction);

	// DaoTransaction を使って、トランザクションを完了する
	transaction.Commit();
}
```

以下の手順でトランザクションを制御します。

* `DaoContext` の `BeginTransaction` メソッドを呼び出し、トランザクションを開始する
* Dao メソッドに `DaoTransaction` を渡す
* `DaoTransaction` の `Commit` メソッドを呼び出し、トランザクションを完了する
