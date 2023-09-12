# 制御構文

## 条件分岐

`True`, 0 以外の数値、空ではないコレクションは真と判定される。
`False`, 0, 空文字、空のコレクション, `None` は偽と判定される。

```python
if CONDITION1:
    pass
elif CONDITION2:
    pass
else:
    pass
```

2 つ以上の比較を 1 つの式で記述できる。

```python
if MIN < value < MAX:
    pass
```

Python3.8 以降で `CONDITION` に代入式が記述できるようになった([PEP 572](https://peps.python.org/pep-0572/))。

パターンマッチは `match` 文で使用できる([PEP 636](https://peps.python.org/pep-0636/))。
パターンは値のアンパックが使用できる。

```python
match VALUE:
    case PATTERN1:
        pass
    case PATTERN2 | PATTERN3:
        pass
    case PATTERN4 if CONDITION:
        pass
    case _:
        pass
```

### 参照

- [if 文](https://docs.python.org/ja/3/reference/compound_stmts.html#the-if-statement)
- [match 文](https://docs.python.org/ja/3/reference/compound_stmts.html#the-match-statement)

## ループ

条件が真の間ループする。ループが break しなければ `else` ブロックが実行される。

```python
while CONTIDION:
    pass
else:
    pass
```

コレクション、イテレータを列挙する。`while` と同様に break しなければ `else` ブロックが実行される。

```python
for val in collection:
    pass
else:
    pass
```

### 参照

- [while 文](https://docs.python.org/ja/3/reference/compound_stmts.html#the-while-statement)
- [for 文](https://docs.python.org/ja/3/reference/compound_stmts.html#the-for-statement)
