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

## リクエスト方式

Dao メソッドには、データベースへのリクエスト方式(SQL 実行方式)を指定する事ができます。
以下の 4 つのリクエスト方式があります。

* Affect
* Single
* SingleOrDefault
* Query

### Affect
影響行の数
整数

### Single
単一行、例外

### SingleOrDefault
単一行

### Query
List

### リクエスト方式の決定方法

1. 戻り値が List の場合、Query
2. Affect 属性が付与されている場合、Affect
3. Single 属性が付与されている場合、Single
4. SingleOrDefault 属性が付与されている場合、SingleOrDefault
5. 戻り値がない場合、Affect
6. 戻り値が整数で、メソッド名が 次の名前で始まる場合、Affect
  * Insert
  * Update
  * Delete
  * Add
  * Set
  * Remove
7. 戻り値が nullable の場合、SingleOrDefault
8. それ以外の場合、Single

## Dao SQL

### Dao SQL の規約

* ファイル名
  * インターフェース名 _ メソッド名 . sql
* パラメーター
  * メソッド引数
* クエリ結果
  * メソッド戻り値

### NDao SQL 記法

* コードコメント
  * --#
* パラメーターコメント
  * /*@引数名*/
* ディレクティブコメント
  * --&

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