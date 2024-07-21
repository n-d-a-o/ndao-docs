# CRUD


## ICrudDao

単純な CRUD のための SQL を書く必要はありません。
CRUD 用の Dao が予め用意されています。
`NDao.Daos.ICrudDao<TEntity>` を使って簡単に CRUD を実行する事ができます。
型パラメーターには、CRUD 対象のエンティティ (テーブル) を指定します。

下記のように使用します。

```csharp
// Pages/Samples.cshtml.cs

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

***ICrudDao メソッド***
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

エンティティは、`NDao.Attributes.Entity` 属性を付与されたクラスであり、データベースのテーブルに相当します。

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

エンティティが紐づくテーブルの名前は、下記の優先順位で決まります。

1. `Table` 属性で指定した名前
2. クラス名 + "s"

#### プロパティ名

プロパティが紐づく列の名前は、下記の優先順位で決まります。

1. `Column` 属性で指定した名前
2. プロパティ名

#### スネークケースを採用している場合

テーブル名や列名にスネークケースを採用している場合、SQL でスネークケースを使用するための設定が必要です。
なお、`NDao.Database.Postgres` は、デフォルトでスネークケースを使用するよう設定されています。


### エンティティの属性

エンティティに関連する下記の属性があります。

***エンティティのクラス属性***
| 属性名 | 属性 | 名前空間 |
|:---|:---|:---|
| Entity | エンティティ宣言 | NDao.Attributes |
| Table | 対応テーブル | System.ComponentModel.DataAnnotations.Schema |

***エンティティのプロパティ属性***
| 属性名 | 属性 | 名前空間 |
|:---|:---|:---|
| Column | 対応列 | System.ComponentModel.DataAnnotations.Schema |
| NotMapped | 非対応列 | System.ComponentModel.DataAnnotations.Schema |
| MaxLength | 最大長 | System.ComponentModel.DataAnnotations |
| SqlDataType | SQL 用データ型 | NDao.Attributes |
| Id | 主キー | NDao.Attributes |
| Generated | データベース生成値 | NDao.Attributes |
| Version | 行バージョン | NDao.Attributes |
| Mask | ログマスク | NDao.Attributes |


### 型

(TODO)


### 主キー

(TODO)


### データベース生成値

(TODO)


### 行バージョン

`Version` 属性が付与されたプロパティは、データの最新状態を管理するためのバージョン列に相当します。
最新でないデータを更新しようとすると `UpdateConcurrencyException` が発生します。
下記の 4 つの方式でバージョンを管理します。

***行バージョンの方式***
| 方式 | 型 | 説明 |
|:---|:---|:---|
| バージョン番号 | 整数 | 更新時に値を 1 加算する。最大値に達すると 0 に戻る。(デフォルト) |
| 更新日時 | string | 更新時に現在日時を設定する。 |
| GUID | string | 更新時に GUID を設定する。 |
| 手動 | 任意 | 自動で値を設定しない。 |

下記のように定義します。

```csharp
// バージョン番号
[Version]
public int Version { get; set; }
```

```csharp
// 更新日時
[Version(RowVersionStrategy.Timestamp)]
public int Version { get; set; }
```

```csharp
// GUID
[Version(RowVersionStrategy.Guid)]
public int Version { get; set; }
```


### ログマスク

(TODO)
