# SQL ファイル実行

## Dao インターフェース

インターフェースに `NDao.Attributes.Dao` 属性を付与すると Dao を生成する事ができます。

```csharp
// Daos/IAccountDao.cs

[Dao]
public interface IAccountDao
{
	Account? GetAccountByEmail(string email);
}
```

### Dao インターフェースの属性

Dao インターフェースに関連する以下の属性が用意されています。

**Dao インターフェースに関連する属性一覧**

| 属性名 | 属性 | 名前空間 |
|:---|:---|:---|
| Dao | Dao インターフェース宣言 | NDao.Attributes |
| Affect | 変更リクエスト | NDao.Attributes |
| SingleOrDefault | 単一行取得リクエスト | NDao.Attributes |
| Single | 単一行取得リクエスト | NDao.Attributes |
| SqlDataType | SQL データ型 (対応予定) | NDao.Attributes |
| Mask | 非ログ出力 (対応予定) | NDao.Attributes |


## Dao メソッド

### SQL 実行方式

Dao メソッドには、以下の 4 つの SQL 実行方式があります。
属性または規約によって制御します。

* Affect
* Query
* Single
* SingleOrDefault

### Affect

非クエリの SQL を実行するための方式です。
戻り値は、影響を与えた行の数です。(戻り値の型が int の場合)
以下の場合、SQL 実行方式として Affect が選択されます。

* `Affect` 属性が付与されている
* 戻り値の型が `void` である
* 戻り値の型が `int`、かつ、メソッド名が `Insert`、`Update`、`Delete` から始まる

以下は、Affect が選択される例です。

```csharp
[Affect]
int UpdateInactiveAccounts();
```

```csharp
void UpdateInactiveAccounts();
```

```csharp
int UpdateInactiveAccounts();
```

```csharp
Task<int> UpdateInactiveAccounts();
```

### Query

複数行を取得するための方式です。
以下の場合、SQL 実行方式として Query が選択されます。

* 戻り値の型が `List<T>` である

以下は、Query が選択される例です。

```csharp
List<Account> GetAccounts();
```

```csharp
Task<List<Account>> GetAccounts();
```

### SingleOrDefault

単一行を取得するための方式です。
該当データが複数ある場合、例外が発生します。
また、該当データがない場合、デフォルト値が戻り値となります。
以下の場合、SQL 実行方式として SingleOrDefault が選択されます。

* `SingleOrDefault` 属性が付与されている
* 戻り値の型が `null` 許容である

以下は、SingleOrDefault が選択される例です。

```csharp
[SingleOrDefault]
Account? GetAccountByEmail(string email);
```

```csharp
Account? GetAccountByEmail(string email);
```

```csharp
Task<Account?> GetAccountByEmail(string email);
```

### Single

単一行を取得するための方式です。
該当データが複数ある場合、例外が発生します。
また、該当データがない場合、例外が発生します。
以下の場合、SQL 実行方式として Single が選択されます。

* `Single` 属性が付与されている
* デフォルト

以下は、Single が選択される例です。

```csharp
[Single]
DateTime GetCurrentTimestamp();
```

```csharp
DateTime GetCurrentTimestamp();
```

```csharp
Task<DateTime> GetCurrentTimestamp();
```

### パラメーター (TODO)

追加の型情報が必要な場合、`SqlDataType` 属性を使用します。


### 戻り値

戻り値の型のプロパティ名またはフィールド名は、クエリ結果のフィールド名と一致させる必要があります。



NDao では、クエリ結果を格納する複合型のオブジェクトを Dto と呼びます。

## Dao SQL


### Dao SQL ファイル

Dao SQL は、以下のファイル名でなければなりません。

```sql
インターフェース名_メソッド名.sql
```


### NDao SQL 記法

Dao SQL では、以下のコメントを用いる事が可能です。

| コメント | 書式 |
|---|---|
| コード コメント | --# コード |
| ディレクティブ コメント | --& ディレクティブ |
| パラメーター コメント | /\*@パラメーター\*/サンプル |


### コードコメント

メソッド呼び出し時に実行する C# コードを記述します。

* `@パラメーター` で、メソッドのパラメーターを参照する事ができます。
* 変数を定義する場合、`_` を先頭または末尾に付ける必要があります。

```sql
--# コード
```

以下はコードコメントの例です。

```sql
--# if (@name is not null) {
	and name = /*@name*/
--# }
```


### ディレクティブコメント

設定などを記述します。

```sql
--& ディレクティブ
```

| ディレクティブ | 書式 | 説明 |
|---|---|---|
| using | --& using 名前空間 | C# の `using` ディレクティブを生成します。 |

以下はディレクティブコメントの例です。

```sql
--& using MyLib.Utilites
...
--# if (MyUtil.Func(@name)) {
	and name = /*@name*/
--# }
```


### パラメーターコメント

パラメーターのプレースホルダーを記述します。
* サンプルは、リテラルのみ指定可能です。
* サンプルは、省略可能です。
* サンプルを記述すると、2 Way SQL として活用する事ができます。

```sql
/*@パラメーター*/サンプル
```

以下はパラメーターコメントの例です。

```sql
and name = /*@name*/'x'
```


### 2 Way SQL

2 Way SQL とは、アプリケーションで実行可能というだけでなく、外部ツールでも実行可能な SQL の事です。
専用のツールで SQL を編集できるためエディターの入力支援を受ける事ができます。
また、プログラム実装前に SQL の構文やロジックを確認する事ができます。

ところで、なぜこのような書式なのでしょうか？
ツールで SQL を実行する場合、パラメーターコメントは単なるコメントであり、当然ながらリテラルは SQL に含まれます。
アプリケーションで実行する場合、NDao はリテラルを除去してコメントをプレースホルダーに変換しています。
そのため、このような書式になっているわけです。
また、サンプルがある事でパラメーターの型や書式が分かり、理解の助けにもなります。

```sql
-- ツールで実行する時
and age = /*@age*/30

-- アプリケーションで実行する時
and age = :age
```

### クエリ結果

クエリ結果のフィールド名は、戻り値の型のプロパティ名またはフィールド名と一致させる必要があります。

データベースオブジェクトの命名にスネークケースを採用している場合、命名に関する設定を変更する必要があります。
なお、`NDao.Database.Postgres` では、デフォルトでスネークケースを使用するよう設定されています。

```csharp
[Dao]
public interface IArticleDao
{
	Article? GetArticle(string id);
}
```

```csharp
public class Article
{
	public string Id { get; set; } = default!;

	public string? Title { get; set; }

	public string? Content { get; set; }
}
```

```sql
select
	 Id
	,Title
	,Content
from
	Articles
...

-- または

select
	 *
from
	Articles
...
```


## Dto (TODO)

エンティティのプロパティを引き継ぐ。

## Dao 実装

以下を開いて Dao 実装を確認する事ができます。

```
ソリューションエクスプローラー --> 依存関係 --> アナライザー --> NDao.DaoGenerator
```
