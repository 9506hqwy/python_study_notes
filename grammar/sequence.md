# シーケンス

## スライス

インデックスで範囲を指定して部分的に取得する。

```text
[start:stop(:step)?]
```

```python
>  [0, 1, 2, 3, 4, 5, 6, 7, 8, 9][1:9:3]
[1, 4, 7]
```

## 比較演算

それぞれの要素を先頭から比較する。

```python
> [1, 2, 0] < [1, 2, 1]
True
> [1, 2, 2] < [1, 2, 1]
False
```

## 代入

`a + b` という新しいリストを作成して `a` に代入する。

```python
> a = [1]
> b = [2]
> a = a + b
> a
[1, 2]
```

`a` に `b` を追加する。

```python
> a = [1]
> b = [2]
> a += b
> a
[1, 2]
```

### 分解

`*` を付けると残りの値が代入できる。

```python
> a, *b = [1, 2, 3]
> a
1
> b
[2, 3]
```

## 辞書

辞書のキーはハッシュ可能なオブジェクトである必要がある([hashable](https://docs.python.org/ja/3/glossary.html#term-hashable))。
ハッシュ可能なオブジェクトは比較(`==`)ができて値が同じオブジェクトは同じハッシュになる。

プリミティブな値、更新不可能なオブジェクトはハッシュ可能なオブジェクトになる。
数字、文字列、更新不可能なオブジェクトで構成されたタプルなどが辞書のキーになる。

### 参照

- [dict](https://docs.python.org/ja/3/library/stdtypes.html#mapping-types-dict)

## タプル

タプルを定義するときに `(`, `)` で囲まなくてもいい。

```python
> 1, 2, 3
(1, 2, 3)
```

### 参照

- [tuple](https://docs.python.org/ja/3/library/stdtypes.html#tuples)
