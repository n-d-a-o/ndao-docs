# SQL ファイル実行

## Dao インターフェース

インターフェースに `NDao.Attributes.Dao` 属性を付与すると Dao インターフェースとなります。
メソッドは SQL ファイルと紐づきます。

```csharp
// Daos/IPersonDao.cs

[Dao]
public interface IPersonDao
{
	Person? GetPersonByAccountId(long accountId);
}
```

### Dao インターフェースの属性

Dao インターフェースに関連する以下の属性が用意されています。

**Dao インターフェースの属性一覧**

| 属性名 | 属性 | 名前空間 |
|:---|:---|:---|
| Dao | Dao インターフェース宣言 | NDao.Attributes |
| Affect | 変更リクエスト | NDao.Attributes |
| SingleOrDefault | 単一行取得リクエスト | NDao.Attributes |
| Single | 単一行取得リクエスト | NDao.Attributes |
| SqlDataType | SQL データ型 (対応予定) | NDao.Attributes |
| Mask | 非ログ出力 (対応予定) | NDao.Attributes |

## Dao メソッド

Dao メソッドには、以下の 4 つの SQL 実行方式があります。
属性または規約によって決定されます。

* Affect
* Query
* Single
* SingleOrDefault

### Affect

非クエリの SQL を実行するための方式です。
戻り値は、影響を与えた行の数です (`void` でない場合)。
以下の場合、SQL 実行方式として `Affect` が選択されます。

* `Affect` 属性が付与されている
* 戻り値の型が `void` である
* 戻り値の型が整数、かつ、メソッド名が次の名前から始まる
  * Insert
  * Update
  * Delete
  * Add
  * Set
  * Remove

以下は、`Affect` が選択される例です。

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
以下の場合、SQL 実行方式として `Query` が選択されます。

* 戻り値の型が List である

以下は、`Query` が選択される例です。

```csharp
List<Account> GetAccounts();
```

```csharp
Task<List<Account>> GetAccounts();
```

### SingleOrDefault

単一行を取得するための方式です。
結果が複数件の場合、例外が発生します。
また、結果が 0 件の場合、デフォルト値を戻り値とします。
以下の場合、SQL 実行方式として `SingleOrDefault` が選択されます。

* SingleOrDefault 属性が付与されている
* 戻り値の型が nullable である

以下は、`SingleOrDefault` が選択される例です。

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
結果が複数件の場合、例外が発生します。
また、結果が 0 件の場合、例外が発生します。
以下の場合、SQL 実行方式として `Single` が選択されます。

* Single 属性が付与されている
* デフォルト

## Dao SQL

### Dao SQL の規約

Dao SQL は、以下の規約に従う必要があります。

**Dao SQL の規約一覧**

| 規約 | 名前 |
|---|---|
| ファイル名 | インターフェース名 _ メソッド名 . sql |
| パラメーター名 | メソッド引数名 |
| 結果フィールド名 | メソッド戻り値の型が複合型の場合、その型のプロパティ名<br>メソッド戻り値の型が単純型の場合、任意の名前 |

```sql

```

### NDao SQL 記法

Dao SQL では、以下のコメントを用いる事が可能です。

| コメント名 | コメント |
|---|---|
| コードコメント | --# CSharp コード |
| パラメーターコメント | /\* @メソッド引数名 \*/ |
| ディレクティブコメント | --& ディレクティブ |


### コードコメント

任意の C# コードを記述する事が可能です。
また、Dao メソッド引数を `@引数名` で参照する事ができます。
if 文を使えば、動的な SQL を作成できます。

```sql
--# if (@name is not null) {
	and name = /*@name*/
--# }
```

### パラメーターコメント

`/*@引数名*/` に続けてリテラルのサンプルデータを記述する事が可能です。
これにより 2 Way SQL として活用する事ができます。

2 Way SQL とは、アプリケーションで実行可能というだけでなく、外部ツールでも実行可能な SQL の事です。
専用のツールで SQL を編集できるためエディターの入力支援を受ける事ができます。
また、プログラム実装前に SQL の構文やロジックを確認する事ができます。

ツールで実行する場合、パラメーターコメントは単なるコメントであり、リテラルが使用されます。
アプリケーションで実行する場合、コメントとリテラルが除去され、ADO.NET のパラメーターとなります。

```sql
	and age = /*@age*/30
```

```sql
	-- アプリケーションで実行する場合
	and age = @age
```

### ディレクティブコメント

設定や補助をするためのコメントです。
現在、利用可能なディレクティブは using ディレクティブのみです。

```sql
--& using MyLib.Utilities.MyUtility
--# MyUtility.Func(@arg);
```

## Dao 実装

以下を開いて確認する事ができます。

```
[ソリューションエクスプローラー]-[依存関係]-[アナライザー]-[NDao.DaoGenerator]
```

## Dto

TODO