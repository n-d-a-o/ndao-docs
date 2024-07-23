# はじめに


## NDao とは

NDao は Dao (Data Access Object) を生成して実行するライブラリです。
ユーザーは Dao インターフェースを通してデータベースを操作しますが、
その実装を作成する必要はありません。
NDao が Dao の実装を提供します。

具体的にどのような事ができるのか見てみましょう。下記は RazorPages のページモデルです。

```csharp
// Pages/Samples.cshtml.cs

public class SamplesModel : PageModel
{
    // Dao インターフェース
    private readonly IPersonDao personDao;

    ...

    public SamplesModel(IPersonDao personDao)
    {
        // DI で Dao インスタンスを受け取る
        this.personDao = personDao;
    }

    public IActionResult OnGet(string? name, int? age)
    {
        ...
        // Dao を使って検索を実行する
        List<Person> persons = personDao.Search(name, age);
        ...

        return Page();
    }
}
```

コンストラクターで Dao インスタンスを受け取り、ページハンドラーで Dao を使って SQL を実行しています。
Dao の定義を見てみましょう。

```csharp
// Daos/IPersonDao.cs

[Dao]
public interface IPersonDao
{
    List<Person> Search(string? name, int? age);
}
```

```sql
-- Daos/IPersonDao_Search.sql

/*
特別なコメントを使って SQL の中に C# のコードを埋め込む事ができます。
if 文などを使えば、動的な SQL も作成できます。
*/

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

インターフェースと SQL ファイルがあるだけです。
SQL ファイルを規約にしたがって配置すると、SQL を実行するための実装クラスが自動で生成されます。
これによりインターフェースのメソッドを呼び出すだけで SQL を実行する事ができます。
要するに、SQL ファイルを関数として呼び出すようなものです。


## 特徴

NDao は下記の特徴を持っています。

* SQL ファイルを簡単に実行する仕組みを提供
* SQL ファイルに C# コードを埋め込む事が可能
* 動的な SQL を作成する事が可能
* 2 Way SQL を作成する事が可能
* ビルド時に SQL 内の C# コードやパラメーターを簡易的にチェックする事が可能
* CRUD 用の API を提供
* SQL のロギングを提供
* EF Core のマイグレーション機能と併用する事が可能


## 前提

下記の環境で使用する事を前提とします。

* .NET 8.0 以降
* Visual Studio 2022 version 17.10 以降
