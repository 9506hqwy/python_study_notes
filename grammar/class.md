# クラス

## `__del__`

インスタンスが解放されるときに呼び出される。

Python3.3 までは `__del__` を実装しているオブジェクトが循環参照の一部になっている場合は解放されなかった。
[PEP 442](https://peps.python.org/pep-0442/) の対応で循環参照でも解放されるようになった。

参照不能になったオブジェクトの一覧を作成し、
参照不能になったオブジェクトの `__del__` を実行したあとに再度参照不能かどうかを判定する。
参照可能なオブジェクトがあれば、それに関連するオブジェクトのガベージコレクトは中断される。
そのため `__del__` が実行されたオブジェクトが解放されているとは限らない。

また、 `__del__` メソッドはオブジェクトに対して一度のみ実行されるようになった。

上記の処理に従うには Native の拡張モジュールは PEP 442 の仕組みに対応する必要がある。

リソースの解放をする場合は `with` や `try-finally` を使用するほうがいい。

## 名前解決

属性やメソッド名の解決は継承順に行われる。
以下の場合は A -> B -> C で検索される。

```python
class A(B, C):
    pass
```

`super()` も同様の順に検索して実行するメソッドが決定される。
そのため基本クラスというわけではなく、兄弟クラスが実行される場合もある。

明示的にクラスを指定してメソッドを実行する場合はクラスを指定する。

```python
C.method(self, ...)
```

また、継承しているクラスで検索順が矛盾する場合はクラス定義時にエラーが発生する。

## クラス変数のスコープ

クラス変数はメソッドのローカル名前空間でもなく、グローバル名前空間にもないため、
メソッド内から直接参照することはできない。

```python
> class A:
.     a = 1
.     def method():
.         return a
.     b = method()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 5, in A
  File "<stdin>", line 4, in method
NameError: name 'a' is not defined
```

## `__slots__`

インスタンスの名前空間として `__dict__` を持つが　`__slots__` を定義すると名前空間として `__slots__` が使用され `__dict__` は存在しなくなる。
また、動的に属性の追加/削除ができなくなる。

## メタクラス

通常は `type` メタクラスを使用してクラスが生成されるが、
`type` を継承してユーザ定義のメタクラスを使用してクラスを生成できる。

```python
class A(metaclass=Meta):
    pass
```

メタクラスの属性はクラスに対する名前解決では使用されるが、インスタンスに対する名前解決では検索されない。

```python
> class Meta(type):
.     def method(self):
.         return 'a'
.
. class A(metaclass=Meta):
.     pass
> A.method()
'a'
> A().method()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'A' object has no attribute 'method'
```

## 抽象基底クラス

`abc.ABCMeta` クラスをメタクラスとして定義したクラスを抽象基底クラスという。

```python
class A(metaclass=abc.ABCMeta):
    pass
```

抽象基底クラスは通常のクラスと同様にインスタンス化や継承などができる。

`@abstractmethod` や `@abstractclassmethod` などは引数は検証対象にならず同じ属性名であればいい。
また、通常のメソッドと同様に実装を記述して `super` で呼び出すことができる。

### 仮想サブクラス

抽象基底クラスを継承関係のないクラスでも `issubclass()` や `isinstance()` などで、
継承しているとして扱うように設定することができる。

仮想サブクラスを登録するには `ABC.register()`, `__subclasshook__` を使用する。

## ディスクリプタ

クラス属性の `__get__`, `__set__`, `__delete__` を実装したオブジェクトをディスクリプタという。

`__get__` のみ実装したディスクリプタを非データディスクリプタ、
`__get__` に加えて `__set__` と `__delete__` を実装したディスクリプタをデータディスクリプタとなる。

データディスクリプタの場合に名前解決はクラスの名前空間、インスタンスの名前空間の順に検索される。

## 特殊メソッド

`__getattr__`, `__getattribute__` は暗黙的に特殊メソッドが呼び出される場合は実行されない。

```python
> class A:
.     def __getattr__(self, name):
.         print("call")
.         return 0
.     def __eq__(self, v):
.         return True
> a1 = A()
> a2 = A()
> a1.__eq__(a2)
True
> a1 == a2
True
> a1.a
call
0
```

`__setattr__` 内で `self.name = value` を実行すると無限ループするので、
`super().__setattr__(name, value)` を実行する。

## データクラス

クラスに自動的に既定の実装を行う `@dataclass` を修飾する。

```python
from dataclasses import dataclass

@dataclass(eq=True, order=True, frozen=True)
class A():
  value: int = 0
```

`eq` で等価演算、
`order` で比較演算、
`frozen` で不変性を実装する。

## プロトコル

オブジェクトのインターフェイスを定義する。
クラスと継承関係がなくても定義したインターフェイスを満たすかどうかをチェックできる。

```python
from typing import Protocol, runtime_checkable

@runtime_checkable
class IA(Protocol):
  def func(self) -> int:
    pass
```

`@runtime_checkable` を修飾することで動的に `isinstance`, `issubclass` で実装しているかどうかを確認できる。
`hasattr` で確認するため引数は確認対象にならない。

```python
> class A:
.   def func(self):
.     return 1
.
. isinstance(A(), IA)
Truec
```

プロトコルを継承したクラスを継承した新たなプロトコルを作成する場合も
必ず `Protocol` を直接継承させる必要がある。

特殊メソッド向けのプロトコルが定義されている。

- `typing.SupportsAbs` (`__abs__`)
- `typing.SupportsBytes` (`__bytes__`)
- `typing.SupportsComplex` (`__complex__`)
- `typing.SupportsFloat` (`__float__`)
- `typing.SupportsIndex` (`__index__`)
- `typing.SupportsInt` (`__int__`)
- `typing.SupportsRound` (`__round__`)
