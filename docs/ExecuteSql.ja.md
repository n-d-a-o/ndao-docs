# SQL ファイル実行


## Dao インターフェース

インターフェースに `NDao.Attributes.Dao` 属性を付与すると Dao インターフェースとなります。
Dao インターフェースと Dao SQL を作成すると自動的に実装クラスが作成されます。

下記のように定義します。

```csharp
// Daos/IAccountDao.cs

[Dao]
public interface IAccountDao
{
	Account? GetAccountByEmail(string email);
}
```

### Dao インターフェースの属性

Dao インターフェースに関連する下記の属性があります。

**Dao インターフェースの属性**

| 属性名 | 属性 | 名前空間 |
|:---|:---|:---|
| Dao | Dao インターフェース宣言 | NDao.Attributes |

**Dao インターフェースのメソッド属性**

| 属性名 | 属性 | 名前空間 |
|:---|:---|:---|
| Affect | 非クエリ実行 | NDao.Attributes |
| SingleOrDefault | 単一行取得クエリ実行 | NDao.Attributes |
| Single | 単一行取得クエリ実行 | NDao.Attributes |

**Dao インターフェースのメソッドパラメーター属性**

| 属性名 | 属性 | 名前空間 |
|:---|:---|:---|
| SqlDataType | SQL 用データ型 (対応予定) | NDao.Attributes |
| Mask | ログ出力保護 (対応予定) | NDao.Attributes |


## Dao メソッド


### SQL 実行方式

Dao メソッドには、下記の 4 つの SQL 実行方式があります。
属性または規約によって決定されます。

* Affect
* Query
* Single
* SingleOrDefault


### Affect

非クエリの SQL を実行する方式です。
戻り値は、影響を与えた行の数です (戻り値の型が int の場合)。
下記の場合、SQL 実行方式として Affect が選択されます。

* `Affect` 属性が付与されている
* 戻り値の型が `void` である
* 戻り値の型が `int`、かつ、メソッド名が `Insert`、`Update`、`Delete` から始まる

Affect の Dao メソッドは、下記のように定義します。

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

複数行を取得する方式です。
下記の場合、SQL 実行方式として Query が選択されます。

* 戻り値の型が `List<T>` である

Query の Dao メソッドは、下記のように定義します。

```csharp
List<Account> GetAccounts();
```

```csharp
Task<List<Account>> GetAccounts();
```


### SingleOrDefault

単一行を取得する方式です。
該当データが複数ある場合、例外が発生します。
また、該当データがない場合、デフォルト値が戻り値となります。
下記の場合、SQL 実行方式として SingleOrDefault が選択されます。

* `SingleOrDefault` 属性が付与されている
* 戻り値の型が `null` 許容である

SingleOrDefault の Dao メソッドは、下記のように定義します。

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

単一行を取得する方式です。
該当データが複数ある場合、例外が発生します。
また、該当データがない場合、例外が発生します。
下記の場合、SQL 実行方式として Single が選択されます。

* `Single` 属性が付与されている
* デフォルト

Single の Dao メソッドは、下記のように定義します。

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

* `SqlDataType`
* `Mask`


### クエリ結果

戻り値の型が複合型の場合、下記の 2 つの名前を一致させます。

* 戻り値の型のプロパティまたはフィールド
* クエリ結果のフィールド

下記のように名前を一致させます。

```csharp
[Dao]
public interface IArticleDao
{
	Article? GetArticle(string id);
}
```

```csharp
// 戻り値の型のプロパティまたはフィールド = Id, Title, Content
public class Article
{
	public string Id { get; set; } = default!;

	public string? Title { get; set; }

	public string? Content { get; set; }
}
```

```sql
-- クエリ結果のフィールド = *, または, Id, Title, Content
select
	 Id
	,Title
	,Content
from
...
```


#### スネークケースの場合

データベースオブジェクトの命名にスネークケースを採用している場合、命名に関する設定を変更する必要があります。
なお、`NDao.Database.Postgres` では、デフォルトでスネークケースを使用するよう設定されています。


## Dao SQL


### Dao SQL ファイル

Dao SQL は、下記のファイル名でなければなりません。

```sql
インターフェース名_メソッド名.sql
```


### NDao SQL 記法

Dao SQL では、下記のコメントを用いる事が可能です。

***NDao SQL 記法***
| コメント | 書式 |
|:---|:---|
| コード コメント | --# コード |
| ディレクティブ コメント | --& ディレクティブ |
| パラメーター コメント | /\*@パラメーター\*/サンプル |


### コードコメント

メソッド呼び出し時に実行する C# コードを記述します。

```sql
--# コード
```

下記のように記述します。

```sql
--# if (@name is not null) {
	and name = /*@name*/
--# }
```

ただし、通常のコードにない記法と制限があります。

***記法***

* `@パラメーター` は、メソッドのパラメーターを参照する。
* `$.メソッド` は、ユーティリティメソッドを呼び出す。

***制限***

* 変数を定義する場合、変数名は、`_` から始める。


### ディレクティブコメント

設定などを記述します。

```sql
--& ディレクティブ
```

下記のように記述します。

```sql
--& using MyLib.Utilites
...
--# if (MyUtil.Func(@name)) {
	and name = /*@name*/
--# }
```

***ディレクティブコメント***
| ディレクティブ | 書式 | 説明 |
|---|---|---|
| using | --& using 名前空間 | C# の `using` ディレクティブを生成する。 |


### パラメーターコメント

パラメーターのプレースホルダーを記述します。
サンプルを記述すると、2 Way SQL として利用する事ができます。

```sql
/*@パラメーター*/サンプル
```

* サンプルは、リテラルのみ指定可能
* サンプルは、省略可能

下記のように記述します。

```sql
and name = /*@name*/'x'
```


### 2 Way SQL

2 Way SQL とは、アプリケーションで実行可能というだけでなく、外部ツールでも実行可能な SQL の事です。
専用のツールで SQL を編集できるためエディターの入力支援を受ける事ができます。
また、プログラム実装前に SQL の構文やロジックを確認する事ができます。

サンプルを記述すると何故 2 Way SQL になるのでしょうか。
ツールで SQL を実行する場合、パラメーターコメントのリテラル以外は単なるコメントであり、リテラルは SQL に含まれます。
アプリケーションで実行する場合、パラメーターコメントのリテラル以外はプレースホルダーに変換され、リテラルは除去されます。
そのため、それぞれ実行可能な SQL となります。

```sql
-- ツールで実行する時
and age = /*@age*/30

-- アプリケーションで実行する時
and age = :age
```


## Dto (TODO)

Dto は、クエリ結果を格納するオブジェクトです。

## Dao 実装

Dao 実装は確認する事ができます。Visual Studio で下記を開きます。

```
ソリューションエクスプローラー --> 依存関係 --> アナライザー --> NDao.DaoGenerator
```
