# 型

## 型システム

Python は強い型付け、動的型付けの言語となる。

## 整数(int 型)

値の範囲はない。システムのメモリが許容する限り表現できる。

### 文字列から変換

文字列、数値から整数型に変換を行う。

```python
> int('1')
1
> int('10', 8)
8
> int('0o10', 0)
8
```

基数が 0 の場合は整数値リテラルとして扱う。

### 2 の補数

Python は負の数を 2 の補数として表現しないので 2 進数に変換してもマイナスは残る。

```python
> bin(-1)
-0b1
```

ビット演算では Python が仮想的に 2 の補数を表現した結果が得られる。

```python
> -1 & 0xFF
255
> print("0b{0:8b}".format(-1 & 0xFF))
0b11111111
```

[long_and](https://github.com/python/cpython/blob/main/Objects/longobject.c#L4854)
などのビット演算から呼び出される
[long_bitwise](https://github.com/python/cpython/blob/main/Objects/longobject.c#L5152)
でビット演算がエミュレートされる。

### ビット反転

整数値の上限がないため Python はビット反転を以下の式で計算している。

```python
def rev_bit(x):
    return -1 * (x + 1)
```

[PyNumber_Invert](https://github.com/python/cpython/blob/main/Objects/abstract.c#L1400C1-L1400C1)
から呼び出される
[long_invert](https://github.com/python/cpython/blob/main/Objects/longobject.c#L4854)
で実装されている。

## 浮動小数点(float 型)

IEEE 754 に従った値となる。

### 文字列から変換

文字列、数値から浮動小数点型に変換を行う。

```python
> float('1')
1.0
```

### 無限大

float 型の範囲外の値は `inf` となり無限大となる。

```python
> float("inf")
inf
```

### 非数

計算不能な場合の結果などは `nan` となり非数となる。

```python
> float("nan")
nan
```

## 論理値(bool 型)

`True` は int 型の 1, `False` は int 型の 0 となる。

`True`, `False` は Python3 から予約語になった。

## 文字列(str 型)とバイト列(bytes 型)

文字列(UNICODE文字のシーケンス)をバイト列に変換することをエンコード、
バイト列を文字列に変換することをデコードという。

```python
> 'a'.encode()
b'a'
> b'a'.decode()
'a'
```

バイト列はエンコードされた値が格納される。
文字列とは違いバイト列と各要素の比較はできない。
バイト列のスライスはバイト列と比較ができる。

```python
> a = b"0"
> a[0]
48
> a[0] == b'0'
False
> a[0] == ord(b'0')
True
> a[:] == b'0'
True
```

## 列挙型

`Enum`, `Flag`, `IntEnum`, `IntFlag`, `Literal` がある。

値は重複可能なので一意にするには `@unique` で修飾する。

### `*Flag`

論理演算が使用できる。

```python
> from enum import Flag, auto
.
. class F(Flag):
.    A = auto()
.    B = auto()
.
. F.A | F.B
<F.A|B: 3>
```

### `Int*`

整数比較ができる。

```python
> from enum import IntEnum
.
. class F(IntEnum):
.    A = 1
.    B = 2
.
. F.A == 1
True
```
