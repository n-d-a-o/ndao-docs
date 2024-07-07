# CRUD

## ICrudDao

典型的な CRUD の SQL を書く必要はありません。
CRUD 用の Dao が予め用意されています。
そのインターフェースである `NDao.Daos.ICrudDao<TEntity>` を通して、簡単に CRUD を実行する事ができます。

テーブルに対応するクラスをエンティティと呼びます。
型パラメーターにエンティティを指定する事で、どのテーブルに対する CRUD を実行するのかを指定します。

`ICrudDao` には以下のメソッドが用意されています。

**ICrudDao メソッド一覧**

| メソッド名 | 説明 |
|:---|:---|
| Get | エンティティを取得します。 |
| Insert | エンティティを追加します。 |
| Update | エンティティを更新します。 | 
| Delete | エンティティを削除します。 |

さらに、それぞれ非同期用のメソッドが用意されています。

使用例を見てみましょう。以下は RazorPages での例です。

```csharp
public class SamplesModel : PageModel
{
	// Dao インターフェース
	private readonly ICrudDao<Person> personCrud;

    public SamplesModel(ICrudDao<Person> personCrud)
	{
		// DI で Dao インスタンスを受け取る
		this.personCrud = personCrud;
	}

	...
}
```

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

```csharp
[Entity]
public class Person
{
	public int Id { get; set; }

	public string Name { get; set; } = default!;

	public int Age { get; set; }
}
```

## エンティティの属性

エンティティに関連する以下の属性が用意されています。

**エンティティのクラス属性一覧**

| 属性名 | 属性 | 名前空間 |
|:---|:---|:---|
| Entity | エンティティ | NDao.Attributes |
| Table | 対応テーブル | System.ComponentModel.DataAnnotations.Schema |

**エンティティのプロパティ属性一覧**

| 属性名 | 属性 | 名前空間 |
|:---|:---|:---|
| Column | 対応列 | System.ComponentModel.DataAnnotations.Schema |
| NotMapped | 非対応列 | System.ComponentModel.DataAnnotations.Schema |
| MaxLength | 最大長 | System.ComponentModel.DataAnnotations |
| SqlDataType | SQL データ型 | NDao.Attributes |
| Id | 主キー列 | NDao.Attributes |
| Generated | データベース生成列 | NDao.Attributes |
| Version | 行バージョン列 | NDao.Attributes |
| Mask | ログ出力マスク | NDao.Attributes |

## エンティティの命名規約

テーブル名は以下の順で決まります。
1. `Table` 属性で指定した名前
2. クラス名 + "s"

列名は以下の順で決まります。
1. `Column` 属性で指定した名前
2. プロパティ名

ただし、スネークケースにするかどうかなどのスタイルはコネクターの設定で指定します。
