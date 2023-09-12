# Tips

## `None` 比較

`==` ではなく `is` を使用して同一性を確認する。

```python
if val == None:
    # == はオーバーライドできるため期待した結果にならない可能性がある。
    pass

if val is None:
    pass
```

`None` はシングルトンオブジェクトとなっている。

## 標準入出力でバイナリ

標準の入出力はテキストエンコーディング処理が行われるため、
エンコーディングを無効化する。

```python
sys.stdin = sys.stdin.detach()
sys.stdout = sys.stdout.detach()
```

## エンコーディング

ファイルシステムからファイル名や環境変数名をバイト列で取得したときに、
`sys.getfilesystemencoding()` でデコードして UNICODE に変換する。

変換時に `surrogateescape` を指定するためロケールの設定が不正な場合でも
エラーにはならない。

`surrogateescape` では変換できない文字を \uDC80 から \uDCFF にマッピングする([PEP 383](https://peps.python.org/pep-0383/))。
これはサロゲートペアの下位領域で本来は \uD800 から \uDBFF の上位領域と合わせて使用する。
`surrogateescape` は上位がなく下位サロゲートのみ使用する。

## 変数 `_`

REPL で最後の評価結果が `_` に格納される([sys.displayhook](https://docs.python.org/ja/3/library/sys.html#sys.displayhook))。

```python
> 1 + 1
> print(_)
2
```

## `Ellipsis` オブジェクト

`...` を記述すると `Ellipsis` オブジェクトになる。

```python
> ...
Ellipsis
```

`Ellipsis` はシングルトンオブジェクトとなっている。

## 識別子名

変数やメソッド名は UNICODE で定められる NFKC で正規化される。

[2.3. 識別子 (identifier) およびキーワード (keyword)](https://docs.python.org/ja/3/reference/lexical_analysis.html#identifiers)

## SCM 変遷

- [PEP 347 – Migrating the Python CVS to Subversion](https://peps.python.org/pep-0347/)
- [PEP 385 – Migrating from Subversion to Mercurial](https://peps.python.org/pep-0385/)
- [PEP 512 – Migrating from hg.python.org to GitHub](https://peps.python.org/pep-0512/)
