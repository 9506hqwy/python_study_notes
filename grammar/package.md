# パッケージ

## 名前空間パッケージ

`__init__.py` が存在しないディレクトリを名前空間パッケージという([PEP 420](https://peps.python.org/pep-0420/))。
名前空間パッケージのサブディレクトリも名前空間パッケージとなる。

名前空間パッケージは別々のディレクトリに分割してモジュールを配置できる。
`package1` を分割して `module1`, `module2` を配置する。

```text
+ Top
  |
  + A
  | |
  | + Package1
  |   |
  |   + module1
  |
  + B
    |
    + Package1
      |
      + module2
```

## `__main__.py`

コンソールから `__main__.py` があるディレクトリや zip ファイルして実行すると `__main__.py` が実行される。

```sh
> python3 -m directory

> python3 module.zip
```

UNIX 環境ならシェバンを付与すれば実行可能バイナリとなる。

```sh
> echo "#!/usr/bin/env python3" > exe
> echo module.zip >> exe
> chmod +x exe
> ./exe
```
