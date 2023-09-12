# 例外

## 構文

```python
try
    pass
except Exception1 as e
    pass
except (Exception2, Exception3) as e
    pass
except* Exception4 as e
    pass
else:
    pass
finally:
    pass
```

### 参照

- [try 文](https://docs.python.org/ja/3/reference/compound_stmts.html#the-try-statement)

## ユーザ定義例外

`BaseException` ではなく `Exception` を継承する。

## 伝播

例外処理中にさらに例外が発生すると両方とも伝播される。

```python
> try:
.     raise Exception("first")
. except:
.     raise Exception("second")
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
Exception: first

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<stdin>", line 4, in <module>
Exception: second
```

例外オブジェクトの `__context__` 属性に処理している例外オブジェクトが格納される。

```python
> try:
.     raise Exception('first')
. except:
.     try:
.         raise Exception('second')
.     except Exception as e:
.         print(e.__context__)
first
```

`__cause__` 属性で伝播する例外を変更する。
例外は自動的に伝搬するため意図的に無効化するには `None` を指定する。

```python
> try:
.     raise Exception("first")
. except Exception:
.     raise Exception("second") from Exception("third")
Exception: third

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "<stdin>", line 4, in <module>
Exception: second
```

## トレースバック

`__traceback__` 属性にトレースバックが格納される。
`with_traceback()` メソッドで例外オブジェクトにトレースバックを設定できる。

## アグリゲーション

複数の例外を投げる([PEP 654](https://peps.python.org/pep-0654/))。

```python
try:
  pass
except Exception:
  raise ExceptionGroup(message, [Exception1, Exception2])
```

## ノート

例外に付随する情報を付加することができる([PEP 678](https://peps.python.org/pep-0678/))。

```python
> e = Exception()
> e.add_note('note')
> raise e
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
Exception
note
```
