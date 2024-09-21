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

## インタプリタと GIL

- [PEP 684 – A Per-Interpreter GIL](https://peps.python.org/pep-0684/)
- [PEP 703 – Making the Global Interpreter Lock Optional in CPython](https://peps.python.org/pep-0703/)
- [PEP 734 – Multiple Interpreters in the Stdlib](https://peps.python.org/pep-0734/)

## リリースサイクル

- [PEP 602 – Annual Release Cycle for Python](https://peps.python.org/pep-0602/)

## 構文パーサ

- [PEP 617 – New PEG parser for CPython](https://peps.python.org/pep-0617/)

## SCM 変遷

- [PEP 347 – Migrating the Python CVS to Subversion](https://peps.python.org/pep-0347/)
- [PEP 385 – Migrating from Subversion to Mercurial](https://peps.python.org/pep-0385/)
- [PEP 512 – Migrating from hg.python.org to GitHub](https://peps.python.org/pep-0512/)
