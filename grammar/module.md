# モジュール

## インポート

`*` でインポートする場合はプレフィックスが _ ではない属性がインポートされる。

```python
from module import *
```

インポートする属性を制限するにはモジュールに `__all__` 属性を定義する。

```python
__all__ = ['func', ...]
```

### `__import__` 関数

`import` 文から [呼び出される](https://github.com/python/cpython/blob/v3.13.1/Python/import.c#L3959)。

- [\_\_import__](https://github.com/python/cpython/blob/v3.13.1/Python/bltinmodule.c#L272-L279)
  - [PyImport_ImportModuleLevelObject](https://github.com/python/cpython/blob/v3.13.1/Python/import.c#L3762)
    - [import_get_module]([import_get_module](https://github.com/python/cpython/blob/v3.13.1/Python/import.c#L204))
      - `sys.modules` からモジュールを取得する。
    - [import_find_and_load](https://github.com/python/cpython/blob/v3.13.1/Python/import.c#L3692)
      - [_find_and_load](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap.py#L1349)
        - `importlib.import_module`　を参照

### `importlib.import_module` 関数

モジュールをインポートする。

- [import_module](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/__init__.py#L88)
  - [_gcd_import](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap.py#L1387)
    - [_find_and_load](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap.py#L1360)
      - [_find_and_load_unlocked](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap.py#L1304)
        - [_find_spec](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap.py#L1262)
          - モジュールを見つける。
        - [module_from_spec](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap.py#L819)
          - モジュールを読み込む。
        - [exec_module](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap_external.py#L1020)
          - モジュールを初期化する。

- [_find_spec](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap.py#L1262)
  - `sys.meta_path` に設定されている `finder` から順番に `spec` を取得する。先頭の `finder` を優先する。
  - 既定で以下が設定されている。
    - [BuiltinImporter.find_spec](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap.py#L985)
    - [FrozenImporter.find_spec](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap.py#L1146)
    - [PathFinder.find_spec](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap_external.py#L1559)
      - [_get_spec](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap_external.py#L1533)
        - `sys.path` にあるモジュールを検索する。
        - [_path_importer_cache](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap_external.py#L1518)
          - `sys.path_hooks` に設定されている `finder` を取得する。
          - 既定で以下が設定されている。
            - zipimport.zipimporter
            - [path_hook](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap_external.py#L1707)
        - [FileFinder.find_spec](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap_external.py#L1666)

- [_load_unlocked](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap.py#L921)
  - [module_from_spec](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap.py#L819)
    - [_init_module_attrs](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap.py#L733)
      - `__XXX__` 属性を設定する。
  - `sys.modules` に保存する。
  - [exec_module](https://github.com/python/cpython/blob/v3.13.1/Lib/importlib/_bootstrap_external.py#L1020)

## モジュール検索パス設定ファイル

`sys.path` に設定されたディレクトリに .pth ファイルがあると、
ファイルに記載されたパスが `sys.path` に追加される。

1行に1つのパスを記述し、相対パスの場合は設定ファイルを基準に記述する。
