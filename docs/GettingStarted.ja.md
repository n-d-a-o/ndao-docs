# はじめに

## NDao とは

NDao は Dao (Data Access Object) を生成して実行するライブラリです。
ユーザーは、Dao インターフェースを通して、データベースを操作しますが、
その実装を作成する必要はありません。
NDao が Dao の実装を提供します。

## Dao の例

具体的にどのような事ができるのか見てみましょう。以下は RazorPages での例です。

```csharp
// Pages/Samples.cshtml.cs

public class SamplesModel : PageModel
{
	// Dao インターフェース
	private readonly IPersonDao personDao;

	public List<Person> Persons { get; private set; } = [];

    public SamplesModel(IPersonDao personDao)
	{
		// DI で Dao インスタンスを受け取る
		this.personDao = personDao;
	}

	public IActionResult OnGet(string? name, int? age)
	{
		if (name is not null || age is not null)
		{
			// Dao を使って検索を実行する
			Persons = personDao.Search(name, age);
		}

		return Page();
	}
}
```

```csharp
// Daos/IPersonDao.cs

public interface IPersonDao
{
	// IPersonDao_Search.sql を同じディレクトリに配置すると、
	// SQL ファイルの内容を実行するためのクラスが自動生成されます。
	List<Person> Search(string? name, int? age);
}
```

```sql
-- Daos/IPersonDao_Search.sql

-- コメントを使って SQL の中に C# のコードを埋め込む事ができます。
-- if 文を使えば、動的な SQL も作成できます。
-- 詳しくは後述します。

select
   *
from
    persons
where
    true = true
--# if (@name is not null) {
    and name like /*@name*/
--# }
--# if (@age is not null) {
    and age = /*@age*/
--# }
;
```

要するに、SQL ファイルを関数として呼び出すようなものです。

## 特徴

NDao は以下のような特徴を持っています。

- SQL ファイルを簡単に実行する仕組みを提供します。
- SQL のパラメーターやコードを実行時ではなくビルド時にエラー検出する事ができます。
- CRUD 用の API を提供します。
- SQL のロギングを提供します。
- EF Core と併用する事が可能です。

## 前提

以下の環境で使用する事を前提とします。

- .NET 8.0 以降
- Visual Studio 2022 version 17.10 以降
