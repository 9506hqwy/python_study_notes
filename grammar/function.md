# 関数

## デフォルト引数

引数の既定値を設定する。
既定値に設定されるオブジェクトは関数オブジェクトが生成されるときに生成され共有する。

```python
def func(a=[]):
    pass
```

同じ関数オブジェクトなら何回 `func` を呼び出しても同じリストが既定値として使用される。

## 位置専用引数

`/` の前に定義されたキーワードは位置専用引数となる。
下記の場合は `a` が位置専用引数となる。`b` は位置またはキーワードどちらにもなる。

```python
def func(a, /, b):
    pass
```

## キーワード専用引数

`*` の後に定義された引数はキーワード専用引数となる。
下記の場合は `b` がキーワード専用引数となる。

```python
def func(*a, b):
    pass

def func(*, b):
    pass
```

## 名前解決

Python3 では関数内のローカル変数の名前解決は静的に行われる。
[事実、ローカルな変数は既に静的に決定されています。](https://docs.python.org/ja/3/tutorial/classes.html#python-scopes-and-namespaces)

関数内部の `from module import *` ができない。
Python2 では警告であった([PEP 227](https://peps.python.org/pep-0227/))が、
Python3 ではエラーになった([What's New In Python 3.0](https://docs.python.org/ja/3/whatsnew/3.0.html#removed-syntax))。

```python
> def func():
.     from math import *
.     return 0
  File "<stdin>", line 2
SyntaxError: import * only allowed at module level
```

また、実行時に解決するグローバル変数を使用するより、
静的に解決するローカル変数を使用するほうが高速になる可能性がある。

## ローカル変数

関数内の関数で外側の関数で定義したローカル変数を更新するには `nonlocal` を使用する([PEP 3104](https://peps.python.org/pep-3104/))。

```python
def method():
    a = 1

    def inner():
        nonlocal a
        a = 2

    return inner
```
