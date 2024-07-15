# CRUD


## ICrudDao

単純な CRUD のための SQL を書く必要はありません。
CRUD 用の Dao が予め用意されています。
そのインターフェースである `NDao.Daos.ICrudDao<TEntity>` を通して簡単に CRUD を実行する事ができます。

型パラメーターにエンティティを指定する事で、どのテーブルに対する CRUD を実行するのかを指定します。
エンティティとは、テーブルに対応するクラスのことです。

下記のように使用します。

```csharp
// Pages/Samples.cshtml.cs (RazorPages)

public class SamplesModel : PageModel
{
	// Dao インターフェース
	private readonly ICrudDao<Person> personCrud;

	public List<Person> Persons { get; private set; } = [];

    public SamplesModel(ICrudDao<Person> personCrud)
	{
		// DI で Dao インスタンスを受け取る
		this.personCrud = personCrud;
	}

	public IActionResult OnGet()
	{
		// ICrudDao メソッドを呼び出す
		Persons = personCrud.GetList();

		return Page();
	}

	...
}
```


### ICrudDao メソッド

`ICrudDao` は下記のメソッドを持ちます。

**ICrudDao メソッド**

| メソッド名 | 説明 |
|:---|:---|
| GetList | エンティティリストを取得する。 |
| GetListAsync | エンティティリストを取得する。(非同期) |
| Get | エンティティを取得する。 |
| GetAsync | エンティティを取得する。(非同期) |
| Insert | エンティティを追加する。 |
| InsertAsync | エンティティを追加する。(非同期) |
| Update | エンティティを更新する。 | 
| UpdateAsync | エンティティを更新する。(非同期) | 
| Delete | エンティティを削除する。 |
| DeleteAsync | エンティティを削除する。(非同期) |

下記のように使用します。

```csharp
// 取得
Person? person = personCrud.Get(id);
```

```csharp
// 追加
Person person = new()
{
	Id = 1,
	Name = "A",
	Age = 25
};
person = personCrud.Insert(person);
```

```csharp
// 更新
Person? person = personCrud.Get(id);
...
person.Name = name;
person = personCrud.Update(person);
```

```csharp
// 削除
Person? person = personCrud.Get(id);
...
person = personCrud.Delete(person);
```


## エンティティ

クラスに `NDao.Attributes.Entity` 属性を付与するとエンティティとなります。
データベースのテーブルに対応するように定義します。

下記のように定義します。

```csharp
[Entity]
public class Person
{
	public int Id { get; set; }

	public string Name { get; set; } = default!;

	public int Age { get; set; }
}
```


### エンティティの命名規約

#### クラス名

対応するテーブル名は下記の順で決まります。

1. `Table` 属性で指定した名前
2. クラス名 + "s"

#### プロパティ名

対応する列名は下記の順で決まります。

1. `Column` 属性で指定した名前
2. プロパティ名

#### スタイル

データベースオブジェクトの命名にスネークケースを採用している場合、設定を変更する必要があります。
`NDao.Database.Postgres` は、デフォルトでスネークケースを使用するよう設定されています。


### エンティティの属性

エンティティに関連する下記の属性があります。

**エンティティのクラス属性**

| 属性名 | 属性 | 名前空間 |
|:---|:---|:---|
| Entity | エンティティ宣言 | NDao.Attributes |
| Table | 対応テーブル | System.ComponentModel.DataAnnotations.Schema |

**エンティティのプロパティ属性**

| 属性名 | 属性 | 名前空間 |
|:---|:---|:---|
| Column | 対応列 | System.ComponentModel.DataAnnotations.Schema |
| NotMapped | 非対応列 | System.ComponentModel.DataAnnotations.Schema |
| MaxLength | 最大長 | System.ComponentModel.DataAnnotations |
| SqlDataType | SQL 用データ型 | NDao.Attributes |
| Id | 主キー列 | NDao.Attributes |
| Generated | データベース生成列 | NDao.Attributes |
| Version | バージョン列 | NDao.Attributes |
| Mask | ログ出力保護 | NDao.Attributes |


### バージョン列

`Version` 属性を付与すると、バージョン列となり、同時実行制御をします。
古くなったデータを更新しようとすると `UpdateConcurrencyException` が発生します。
下記の 4 つの方式があります。

**同時実行制御の方式**

| 方式 | 型 | 説明 |
|:---|:---|:---|
| 番号 | 整数 | 更新時に値を 1 加算する。最大値に達すると 0 に戻る。(デフォルト) |
| 日時 | string | 更新時に現在日時を設定する。 |
| GUID | string | 更新時に GUID を設定する。 |
| 手動 | 任意 | 自動で値を設定しない。 |

下記のように定義します。

```csharp
// 番号
[Version]
public int Version { get; set; }
```

```csharp
// 日時
[Version(RowVersionStrategy.Time)]
public int Version { get; set; }
```

```csharp
// GUID
[Version(RowVersionStrategy.Guid)]
public int Version { get; set; }
```
