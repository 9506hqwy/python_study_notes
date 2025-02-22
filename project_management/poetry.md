# poetry

パッケージ管理、依存関係管理、タスクランナ機能があるプロジェクト管理ツールである。
フォーマッタ、リンタ、テストはタスクランナ機能を利用する。

複数の仮想環境を操作する機能はないが、
`poetry env use` コマンドで仮想環境の Python バージョンを切り替えることができる。

## インストール

インストールスクリプトを実行する。

```sh
> curl -sSL https://install.python-poetry.org | python3 -
Retrieving Poetry metadata

# Welcome to Poetry!

This will download and install the latest version of Poetry,
a dependency and package manager for Python.

It will add the `poetry` command to Poetry's bin directory, located at:

/root/.local/bin

You can uninstall at any time by executing this script with the --uninstall option,
and these changes will be reverted.

Installing Poetry (2.1.1): Done

Poetry (2.1.1) is installed now. Great!

To get started you need Poetry's bin directory (/root/.local/bin) in your `PATH`
environment variable.

Add `export PATH="/root/.local/bin:$PATH"` to your shell configuration file.

Alternatively, you can call Poetry explicitly with `/root/.local/bin/poetry`.

You can test that everything is set up by executing:

`poetry --version`
```

## バージョン

```sh
> poetry --version
Poetry (version 2.1.1)
```

## プロジェクトの作成

プロジェクトを作成する。

```sh
> poetry init
```

例: [sampleproject](https://github.com/pypa/sampleproject) を管理下にする。
仮想環境はプロジェクトディレクトリに配置する。

```sh
> poetry config virtualenvs.in-project true --local
> poetry init
A pyproject.toml file with a project and/or a poetry section already exists.
```

src レイアウトのためパッケージを指定する。

```toml
[tool.poetry]
packages = [
    {include = "sample", from = "src"},
]
```

依存関係をインストールする。

```sh
> poetry install --all-groups
Updating dependencies
Resolving dependencies... (0.6s)

Package operations: 1 install, 0 updates, 0 removals

  - Installing peppercorn (0.6)

Writing lock file

Installing the current project: sampleproject (4.0.0)
```

`sampleproject` が editable モードでインストールされる。

```sh
> poetry run pip list
Package       Version Editable project location
------------- ------- -------------------------
peppercorn    0.6
pip           25.0.1
sampleproject 4.0.0   /root/sampleproject
```

*poetry.lock* が作成される。

## 環境の作成

環境に入る。
初回実行時に仮想環境が作成される。

```sh
> $(poetry env activate)
Creating virtualenv sampleproject in /root/sampleproject/.venv
(sampleproject-py3.13) >
```

インタプリタをインストールする。
`python.installation-dir` にインストールされる。

```sh
> poetry python install 3.12
Downloading and installing 3.12 (cpython) ... Done
Testing 3.12 (cpython) ... Done
```

## 依存関係

任意のグループを指定して依存関係を追加することはできる。

`project.dependencies` は暗黙的に `tool.poetry.group.main` となる。

*poetry.lock* 内でグループを区別するため、グループを選択的にインストールすることができる。

### オプショナルなパッケージを追加

グループ dev にパッケージを追加する。

```sh
> poetry add -D ruff
Using version ^0.9.7 for ruff

Updating dependencies
Resolving dependencies... (0.3s)

Package operations: 1 install, 0 updates, 0 removals

  - Installing ruff (0.9.7)

Writing lock file
```

*pyproject.tml* に下記が追加される。

```toml
[tool.poetry.group.dev.dependencies]
ruff = "^0.9.7"
```

*poetry.lock* が更新される。

### ローカルファイルのパッケージを追加

*pyproject.toml* に相対パスでローカルにあるパッケージを指定する。

poetry は `project.dependencies` で相対パスをサポートしていないため
`tool.poetry.dependencies` を使用する。

プロジェクトを作成する。

```sh
> poetry new --src --name sample2 sampleproject2
The --src option is now the default and will be removed in a future version.
Created package sample2 in sampleproject2
```

PyPI の依存関係を追加する。

```sh
> poetry add peppercorn
Using version ^0.6 for peppercorn

Updating dependencies
Resolving dependencies... (0.1s)

Package operations: 1 install, 0 updates, 0 removals

  - Installing peppercorn (0.6)

Writing lock file
```

ローカルファイル *lib/sampleproject-4.0.0-py3-none-any.whl* の依存関係を追加する。

```sh
> poetry add ./lib/sampleproject-4.0.0-py3-none-any.whl

Updating dependencies
Resolving dependencies... (0.1s)

Package operations: 1 install, 0 updates, 0 removals

  - Installing sampleproject (4.0.0 /root/sampleproject2/lib/sampleproject-4.0.0-py3-none-any.whl)

Writing lock file
```

*pyproject.toml* に下記が追加される。

```toml
[project]
dependencies = [
    "sampleproject @ file:///root/sampleproject2/lib/sampleproject-4.0.0-py3-none-any.whl"
]
```

上記を削除し `tool.poetry.dependencies` を使用する。

```toml
[project]
dependencies = [
    "sampleproject"
]

[tool.poetry.dependencies]
sampleproject = { path = "./lib/sampleproject-4.0.0-py3-none-any.whl" }
```

```sh
> poetry lock
Resolving dependencies... (0.1s)

Writing lock file
```

## ビルド

パッケージを生成する。

```sh
> poetry build
Building sampleproject (4.0.0)
Building sdist
Building wheel
```

sdist に含まれるファイルを確認する。

```sh
> tar -tf dist/sampleproject-4.0.0.tar.gz
sampleproject-4.0.0/
sampleproject-4.0.0/LICENSE.txt
sampleproject-4.0.0/PKG-INFO
sampleproject-4.0.0/README.md
sampleproject-4.0.0/pyproject.toml
sampleproject-4.0.0/setup.cfg
sampleproject-4.0.0/src/
sampleproject-4.0.0/src/sample/
sampleproject-4.0.0/src/sample/__init__.py
sampleproject-4.0.0/src/sample/package_data.dat
sampleproject-4.0.0/src/sample/simple.py
sampleproject-4.0.0/src/sampleproject.egg-info/
sampleproject-4.0.0/src/sampleproject.egg-info/PKG-INFO
sampleproject-4.0.0/src/sampleproject.egg-info/SOURCES.txt
sampleproject-4.0.0/src/sampleproject.egg-info/dependency_links.txt
sampleproject-4.0.0/src/sampleproject.egg-info/entry_points.txt
sampleproject-4.0.0/src/sampleproject.egg-info/requires.txt
sampleproject-4.0.0/src/sampleproject.egg-info/top_level.txt
sampleproject-4.0.0/tests/
sampleproject-4.0.0/tests/test_simple.py
```

wheel に含まれるファイルを確認する。

```sh
> unzip -l dist/sampleproject-4.0.0-py3-none-any.whl
Archive:  dist/sampleproject-4.0.0-py3-none-any.whl
  Length      Date    Time    Name
---------  ---------- -----   ----
      111  2025-02-22 01:55   sample/__init__.py
        9  2025-02-22 01:55   sample/package_data.dat
       43  2025-02-22 01:55   sample/simple.py
     1081  2025-02-22 02:55   sampleproject-4.0.0.dist-info/LICENSE.txt
     4392  2025-02-22 02:55   sampleproject-4.0.0.dist-info/METADATA
       91  2025-02-22 02:55   sampleproject-4.0.0.dist-info/WHEEL
       39  2025-02-22 02:55   sampleproject-4.0.0.dist-info/entry_points.txt
        7  2025-02-22 02:55   sampleproject-4.0.0.dist-info/top_level.txt
      742  2025-02-22 02:55   sampleproject-4.0.0.dist-info/RECORD
---------                     -------
     6515                     9 files
```

## フォーマット

3rd Party のフォーマッタを追加して実行する。

```sh
> poetry add -D ruff
```

ソースコードをフォーマットする。

```sh
> poetry run ruff format
2 files reformatted, 3 files left unchanged
```

## リント

3rd Party のリンタを追加して実行する。

```sh
> poetry add -D ruff
```

ソースコードを静的解析する。

```sh
> poetry run ruff check --fix
All checks passed!
```

## テスト

`pytest` をプロジェクトに追加する。

```sh
> poetry add -D pytest pytest-cov
```

設定を追加する。

```sh
> cat >>pyproject.toml <<EOF

[tool.pytest.ini_options]
addopts = ["--cov=src", "-v"]
EOF
```

テストを実行する。

```sh
> poetry run pytest
========================================================================================= test session starts =========================================================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0 -- /root/sampleproject/.venv/bin/python
cachedir: .pytest_cache
rootdir: /root/sampleproject
configfile: pyproject.toml
plugins: cov-6.0.0
collected 1 item

tests/test_simple.py::TestSimple::test_add_one PASSED                                                                                                                                           [100%]

---------- coverage: platform linux, python 3.13.2-final-0 -----------
Name                     Stmts   Miss  Cover
--------------------------------------------
src/sample/__init__.py       2      1    50%
src/sample/simple.py         2      0   100%
--------------------------------------------
TOTAL                        4      1    75%


========================================================================================== 1 passed in 0.05s ==========================================================================================
```

複数の環境を指定してテストは実行できないので 3rd Party のタスクランナで実行する。
必要なインタプリタもシステムにインストールする。

```sh
> cat >>pyproject.toml <<EOF

[tool.tox]
legacy_tox_ini = """
[tox]
env_list =
    py311
    py312

[testenv]
allowlist_externals = poetry
skip_install = true
commands =
    poetry sync --with=dev
    pytest --cov-append
"""
EOF
```

テストを実行する。

```sh
> tox
py311: commands[0]> poetry sync --with=dev
Installing dependencies from lock file

Package operations: 8 installs, 0 updates, 2 removals

  - Removing setuptools (75.8.0)
  - Removing wheel (0.45.1)
  - Installing iniconfig (2.0.0)
  - Installing packaging (24.2)
  - Installing pluggy (1.5.0)
  - Installing coverage (7.6.12)
  - Installing pytest (8.3.4)
  - Installing peppercorn (0.6)
  - Installing ruff (0.9.7)
  - Installing pytest-cov (6.0.0)

Installing the current project: sampleproject (4.0.0)
py311: commands[1]> pytest --cov-append
========================================================================================= test session starts =========================================================================================
platform linux -- Python 3.11.2, pytest-8.3.4, pluggy-1.5.0 -- /root/sampleproject/.tox/py311/bin/python
cachedir: .tox/py311/.pytest_cache
rootdir: /root/sampleproject
configfile: pyproject.toml
plugins: cov-6.0.0
collected 1 item

tests/test_simple.py::TestSimple::test_add_one PASSED                                                                                                                                           [100%]

---------- coverage: platform linux, python 3.11.2-final-0 -----------
Name                     Stmts   Miss  Cover
--------------------------------------------
src/sample/__init__.py       2      1    50%
src/sample/simple.py         2      0   100%
--------------------------------------------
TOTAL                        4      1    75%


========================================================================================== 1 passed in 0.02s ==========================================================================================
py311: OK ? in 2.21 seconds
py312: skipped because could not find python interpreter with spec(s): py312
  py311: OK (2.21=setup[0.17]+cmd[1.68,0.36] seconds)
  py312: SKIP (0.01 seconds)
  congratulations :) (2.27 seconds)
```

poetry が管理するインタプリタを使用できないか？
