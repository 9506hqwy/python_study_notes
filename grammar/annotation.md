# アノテーション

変数、関数の引数や戻り値に注釈が付けられる([PEP 3107](https://peps.python.org/pep-3107/))。

```python
def func(a: "引数1", b: "引数2") -> "戻り値":
    pass
```

ヘルプで以下のように表示される。

```python
> help(func)
Help on function func in module __main__:

func(a: '引数1', b: '引数2') -> '戻り値'
```

## 型アノテーション

型アノテーションはアノテーションに型情報を埋め込んで型ヒントとして使用する([PEP 484](https://peps.python.org/pep-0484/))。
型ヒントは実行には何の影響も与えない。

```python
def func(a: int, b: str) -> str:
    return str(a) + b
```

### `NewType`

ある型から交換不可能な別の型を作成する。
エイリアスと同様だがエイリアスは交換可能となる。

型 `A` から型 `B` を作成する場合は以下のように定義する。
型 `A` に特定の制約を付けて区別する目的で使用する。

```python
from typing import NewType

B = NewType("B", A)
```

### `TypeVar`

型パラメータを作成する。

```python
from typing import TypeVar

T = TypeVar("T")

def func(v: T) -> [T]:
    return [v]
```

Python 3.12 以降は `TypeVar` を以下の形式で記述できる ([PEP 695](https://peps.python.org/pep-0695/))。

```python
def func[T](v: T) -> [T]:
    return [v]
```

### `Never` と `NoReturn`

値を持たない型を示す。
Python 3.11 以降では例外を返却する関数は `Never` を使用する必要がある。

### `Union`

複数の型のうちいずれかの変数を示す。

`int` 型の値はまたは `float` 型の値を返却する関数の場合は以下のように定義する。

```python
from typing import Union

def func() -> Union[int, float]:
    return 1.0
```

Python 3.10 以降は `Union` を以下の形式で記述できる。

```python
def func() -> int | float:
    return 1.0
```

### `Optional`

None 許容の変数を示す。

`int` 型の値または `None` を返却する関数の場合は以下のように定義する。

```python
from typing import Optional

def func() -> Optional[int]:
    return None
```

Python 3.10 以降は `Optional` を以下の形式で記述できる。

```python
def func() -> int | None:
    return 1.0
```

### `Concatenate`

型を連結する。

Python 3.11 時点で Callable の最初の引数としてのみ有効となる。

### `Lietral`

リテラルを示す。

```python
from typing import Literal

li = Literal["A", "B"]
value: li = "A"
```

### `Final`

不変な変数を示す。

### `Annotated`

型に制約を付ける。

```python
from typing import Annotated
from dataclasses import dataclass

@dataclass
class ValueRange:
    lo: int
    hi: int

Annotated[int, ValueRange(0, 9)]
```

### `TypeGuard`

関数の bool 値の戻り値を修飾する。
`True` の場合に `TypeGuard` に指定した型が関数の引数の型となることを示す。

```python
def is_str(v: object) -> TypeGuard[str]:
    return isinstance(v, str)
```

上記の場合に `True` となるとき `v` は型チェッカに `str` と推定される。

### `TypedDict`

`dict` に型チェック用の型情報を付与する。
実行時には何の影響も与えない。

## 型情報の配布

型情報の配布方法は [PEP 561](https://peps.python.org/pep-0561/) で定義されている。

パッケージに同封するインラインと、型情報のみ配布するスタブがある。

### インライン

パッケージ内にマーカーファイル *py.types* を追加する。
パッケージのルートディレクトリに配置すると再帰的に型情報が配布される。
マーカーファイルは空でいい。

### スタブ

型情報(`*.pyi`)のみ別のパッケージにして配布する。パッケージ名は `<パッケージ>-stubs` とする必要がある。
マーカーファイル *py.typed* を追加する必要はない。

### 型情報の解決順序

1. ユーザが追加したスタブや Python
1. 実装中のコード
1. スタブパッケージ(`*-stubs`)の型情報
1. インライン型情報
1. `Typeshed`
