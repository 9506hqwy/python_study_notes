# エンコーディング

## ファイル

ファイルの read / write は `encoding` 引数でエンコーディングを指定する。
指定しない場合は `locale.getpreferredencoding()` が既定値となる。

## バイト列

文字列(`str.encoding`)とバイト列(`bytes.decode`)の変換は `sys.getdefaultencoding()` が既定値となる。

## 標準入出力

環境変数 `PYTHONIOENCODING` で指定する。

Windows でコンソール入出力の場合は環境変数 `PYTHONLEGACYWINDOWSSTDIO` を指定して ASCII API を使用する場合のみ影響を受ける ([PEP 528](https://peps.python.org/pep-0528/))。
ファイルなどにリダイレクトするときは環境変数 `PYTHONLEGACYWINDOWSSTDIO` に関係なく影響を受ける。

## ファイル名と環境変数

ファイルシステムからファイル名や環境変数名をバイト列で取得したときに、
`sys.getfilesystemencoding()` でデコードして UNICODE に変換する。

変換時に `surrogateescape` を指定するためロケールの設定が不正な場合でも
エラーにはならない。

`surrogateescape` では変換できない文字を \uDC80 から \uDCFF にマッピングする([PEP 383](https://peps.python.org/pep-0383/))。
これはサロゲートペアの下位領域で本来は \uD800 から \uDBFF の上位領域と合わせて使用する。
`surrogateescape` は上位がなく下位サロゲートのみ使用する。

## UTF-8 モード

Python 3,7 以降でエンコーディングを UTF-8 に設定する。
Python 3.15 以降は既定で UTF-8 モードが有効になる。

通常はシステムロケールに従う。

```sh
> python -c "import locale; print(locale.getpreferredencoding(False))"
UTF-8
```

```sh
> LANG=ja_JP.eucJP python -c "import locale; print(locale.getpreferredencoding(False))"
EUC-JP
```

UTF-8 モードを有効な場合はシステムロケールに関係なく UTF-8 になる。

```sh
> LANG=ja_JP.eucJP PYTHONUTF8=1 python -c "import locale; print(locale.getpreferredencoding(False))"
UTF-8
```

UTF-8 モードに関係なくロケールを取得するには `locale.getencoding()` を使用する。
`locale.getpreferredencoding(False)` は影響を受ける。

Python 3.11 以前は`encoding="locale"` は utf-8 となる。

## 参考

- [PEP 528 – Change Windows console encoding to UTF-8](https://peps.python.org/pep-0528/)
- [PEP 529 – Change Windows filesystem encoding to UTF-8](https://peps.python.org/pep-0529/)
- [PEP 540 – Add a new UTF-8 Mode](https://peps.python.org/pep-0540/)
- [PEP 597 – Add optional EncodingWarning](https://peps.python.org/pep-0597/)
- [PEP 686 – Make UTF-8 mode default](https://peps.python.org/pep-0686/)
