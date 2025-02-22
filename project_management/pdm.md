# PDM

パッケージ管理、依存関係管理、仮想環境管理、タスクランナ機能があるプロジェクト管理ツールである。
フォーマッタ、リンタ、テストはタスクランナ機能を利用する。

## インストール

インストールスクリプトを実行する。

```sh
> curl -sSL https://pdm-project.org/install-pdm.py | python3 -
Installing PDM (latest): Creating virtual environment
Installing PDM (latest): Installing PDM and dependencies
Installing PDM (latest): Making binary at /root/.local/bin
Usage: pdm [-h] [-V] [-c CONFIG] [-v | -q] [--no-cache] [-I] [--pep582 [SHELL]] [-n] ...

    ____  ____  __  ___
   / __ \/ __ \/  |/  /
  / /_/ / / / / /|_/ /
 / ____/ /_/ / /  / /
/_/   /_____/_/  /_/

Options:
  -h, --help            Show this help message and exit.
  -V, --version         Show the version and exit
  -c, --config CONFIG   Specify another config file path [env var: PDM_CONFIG_FILE]
  -v, --verbose         Use `-v` for detailed output and `-vv` for more detailed
  -q, --quiet           Suppress output
  --no-cache            Disable the cache for the current command. [env var: PDM_NO_CACHE]
  -I, --ignore-python   Ignore the Python path saved in .pdm-python. [env var: PDM_IGNORE_SAVED_PYTHON]
  --pep582 [SHELL]      Print the command line to be eval'd by the shell for PEP 582
  -n, --non-interactive
                        Don't show interactive prompts but use defaults. [env var: PDM_NON_INTERACTIVE]

Commands:
  add                   Add package(s) to pyproject.toml and install them
  build                 Build artifacts for distribution
  cache                 Control the caches of PDM
  completion            Generate completion scripts for the given shell
  config                Display the current configuration
  export                Export the locked packages set to other formats
  fix                   Fix the project problems according to the latest version of PDM
  import                Import project metadata from other formats
  info                  Show the project information
  init                  Initialize a pyproject.toml for PDM. Built-in templates: - default: `pdm init`, A simple template with a basic structure. - minimal: `pdm init minimal`, A minimal template
                        with only `pyproject.toml`.
  install               Install dependencies from lock file
  list                  List packages installed in the current working set
  lock                  Resolve and lock dependencies
  outdated              Check for outdated packages and list the latest versions on indexes.
  publish               Build and publish the project to PyPI
  python (py)           Manage installed Python interpreters
  remove                Remove packages from pyproject.toml
  run                   Run commands or scripts with local packages loaded
  search                Search for PyPI packages
  self (plugin)         Manage the PDM program itself (previously known as plugin)
  show                  Show the package information
  sync                  Synchronize the current working set with lock file
  update                Update package(s) in pyproject.toml
  use                   Use the given python version or path as base interpreter. If not found, PDM will try to install one.
  venv                  Virtualenv management

Successfully installed: PDM (2.22.3) at /root/.local/bin/pdm
Post-install: Please add /root/.local/bin to PATH by executing:
    export PATH=/root/.local/bin:$PATH
```

## バージョン

```sh
> pdm --version
PDM, version 2.22.3
```

## プロジェクトの作成

プロジェクトを作成する。

```sh
> pdm init
```

例: [sampleproject](https://github.com/pypa/sampleproject) を管理下にする。

```sh
> pdm init
pyproject.toml already exists, update it now.
Please enter the Python interpreter to use
 0. cpython@3.13 (/usr/local/bin/python)
 1. cpython@3.13 (/usr/local/bin/python3.13)
 2. cpython@3.11 (/usr/bin/python3.11)
Please select (0):
Virtualenv is created successfully at /root/sampleproject/.venv
Project name (sampleproject):
Project version (0.1.0):
Do you want to build this project for distribution(such as wheel)?
If yes, it will be installed by default when running `pdm install`. [y/n] (n): y
Project description ():
Which build backend to use?
0. pdm-backend
1. setuptools
2. flit-core
3. hatchling
Please select (0):
License(SPDX name) (MIT):
Author name ():
Author email ():
Python requires('*' to allow any) (>=3.13):
Project is initialized successfully
```

以下の設定が更新される(他の値も更新されるが PDM 関係のみ)。

```toml
[build-system]
requires = ["pdm-backend"]
build-backend = "pdm.backend"

[tool.pdm]
distribution = true
```

*./.pdm-python* が作成される。

```text
/root/sampleproject/.venv/bin/python
```

プロジェクト名と同じ名前のソースコードディレクトリが作成されるため削除する。

```sh
> rm -rf src/sampleproject
```

`project.license` の記載でエラーが発生するため下記に修正する。

```toml
[project]
license = { file = "LICENSE.txt" }
```

依存関係をインストールする。

```sh
> pdm install --with :all
WARNING: Lockfile does not exist
Updating the lock file...
Changes are written to pdm.lock.
  0:00:03 ? Lock successful.
Synchronizing working set with resolved packages: 7 to add, 0 to update, 0 to remove

  ? Install setuptools 75.8.0 successful
  ? Install pyproject-hooks 1.2.0 successful
  ? Install peppercorn 0.6 successful
  ? Install build 1.2.2.post1 successful
  ? Install check-manifest 0.50 successful
  ? Install packaging 24.2 successful
  ? Install coverage 7.6.12 successful
  ? Install sampleproject 0.1.0 successful

  0:00:01 ? All complete! 7/7
```

`sampleproject` が editable モードでインストールされる。
仮想環境内の `pip list` では表示されない。

```sh
> pdm list --csv
name,version,location
build,1.2.2.post1,
check-manifest,0.50,
coverage,7.6.12,
packaging,24.2,
peppercorn,0.6,
pyproject_hooks,1.2.0,
sampleproject,0.1.0,-e /root/sampleproject
setuptools,75.8.0,
```

*pdm.lock* が作成される。

## 環境の作成

環境に入る。

```sh
> $(pdm venv activate)
(sampleproject-3.13) >
```

インタプリタをインストールする。

```sh
> pdm python install 3.12
Successfully installed cpython@3.12.8
Version: 3.12.8
Executable: /root/.local/share/pdm/python/cpython@3.12.8/bin/python3
```

インタプリタを切り替える(仮想環境を切り替える)。

```sh
> pdm use
```

プロジェクト固有のインタプリタ以外に切り替えるとパッケージを共有する。

## 依存関係

任意のグループを指定して依存関係を追加することはできる。

`project.dependencies` は `--prod` となる。

`project.optional-dependencies.<GROUP>` と `tool.pdm.dev-dependencies.<GROUP>` を
使用できるが同じ名前のグループ名があると期待通りに動作しない。

*pdm.lock* 内でグループを区別するため、グループを選択的にインストールすることができる。

### オプショナルなパッケージを追加

グループ dev にパッケージを追加する。

```sh
> pdm add -d ruff
Adding packages to dev dev-dependencies: ruff
  0:00:03 ? Lock successful.
Changes are written to pyproject.toml.
Synchronizing working set with resolved packages: 1 to add, 0 to update, 0 to remove

  ? Install ruff 0.9.7 successful

  0:00:02 ? All complete! 1/1
```

*pyproject.tml* に下記が追加される。

```toml
[project.optional-dependencies]
dev = [
    "ruff>=0.9.7",
]
```

グループ testing にパッケージを追加する。

```sh
> pdm add -d -G testing pytest coverage
INFO: Adding group testing to lockfile
Adding packages to testing dev-dependencies: pytest, coverage
  0:00:04 ? Lock successful.
Changes are written to pyproject.toml.
Synchronizing working set with resolved packages: 3 to add, 0 to update, 0 to remove

  ? Install iniconfig 2.0.0 successful
  ? Install pluggy 1.5.0 successful
  ? Install pytest 8.3.4 successful

  0:00:00 ? All complete! 3/3
```

*pyproject.tml* に下記が追加される。

```toml
[dependency-groups]
testing = [
    "pytest>=8.3.4",
    "coverage>=7.6.12",
]
```

*pdm.lock* が作成される。

### ローカルファイルのパッケージを追加

*pyproject.toml* に相対パスでローカルにあるパッケージを指定する。

プロジェクトを作成する。

```sh
> mkdir sampleproject2
> cd sampleproject2
> pdm init minimal
Creating a pyproject.toml for PDM...
Please enter the Python interpreter to use
 0. cpython@3.13 (/usr/local/bin/python)
 1. cpython@3.13 (/usr/local/bin/python3.13)
 2. cpython@3.12 (/root/.local/share/pdm/python/cpython@3.12.8/bin/python3)
 3. cpython@3.11 (/usr/bin/python3.11)
Please select (0):
Virtualenv is created successfully at /root/sampleproject2/.venv
Project name (sampleproject2):
Project version (0.1.0):
Do you want to build this project for distribution(such as wheel)?
If yes, it will be installed by default when running `pdm install`. [y/n] (n): y
Project description ():
Which build backend to use?
0. pdm-backend
1. setuptools
2. flit-core
3. hatchling
Please select (0):
License(SPDX name) (MIT):
Author name ():
Author email ():
Python requires('*' to allow any) (>=3.13): >=3.11
Project is initialized successfully
> touch README.md
```

PyPI の依存関係を追加する。

```sh
> pdm add peppercorn
Adding packages to default dependencies: peppercorn
  0:00:00 ? Lock successful.
Changes are written to pyproject.toml.
Synchronizing working set with resolved packages: 1 to add, 0 to update, 0 to remove

  ? Install peppercorn 0.6 successful
  ? Update sampleproject2 0.1.0 -> 0.1.0 successful

  0:00:00 ? All complete! 1/1
```

ローカルファイル *lib/sampleproject-0.1.0-py3-none-any.whl* の依存関係を追加する。

```sh
> pdm add ./lib/sampleproject-0.1.0-py3-none-any.whl
Adding packages to default dependencies: file:///${PROJECT_ROOT}/lib/sampleproject-0.1.0-py3-none-any.whl
  0:00:00 ? Lock successful.
Changes are written to pyproject.toml.
Synchronizing working set with resolved packages: 1 to add, 0 to update, 0 to remove

  ? Install sampleproject 0.1.0 successful
  ? Update sampleproject2 0.1.0 -> 0.1.0 successful

  0:00:00 ? All complete! 1/1
```

*pyproject.toml* に下記が追加される。

```toml
[project]
dependencies = [
    "sampleproject @ file:///${PROJECT_ROOT}/lib/sampleproject-0.1.0-py3-none-any.whl"
]
```

## ビルド

パッケージを生成する。

```sh
> pdm build
Building sdist...
Built sdist at /root/sampleproject/dist/sampleproject-0.1.0.tar.gz
Building wheel from sdist...
Built wheel at /root/sampleproject/dist/sampleproject-0.1.0-py3-none-any.whl
```

sdist に含まれるファイルを確認する。

```sh
> tar -tf dist/sampleproject-0.1.0.tar.gz
sampleproject-0.1.0/LICENSE.txt
sampleproject-0.1.0/README.md
sampleproject-0.1.0/pyproject.toml
sampleproject-0.1.0/src/sample/__init__.py
sampleproject-0.1.0/src/sample/package_data.dat
sampleproject-0.1.0/src/sample/simple.py
sampleproject-0.1.0/tests/__init__.py
sampleproject-0.1.0/tests/test_simple.py
sampleproject-0.1.0/PKG-INFO
```

wheel に含まれるファイルを確認する。

```sh
> unzip -l dist/sampleproject-0.1.0-py3-none-any.whl
Archive:  dist/sampleproject-0.1.0-py3-none-any.whl
  Length      Date    Time    Name
---------  ---------- -----   ----
      111  2016-01-01 00:00   sample/__init__.py
        9  2016-01-01 00:00   sample/package_data.dat
       43  2016-01-01 00:00   sample/simple.py
     4438  2016-01-01 00:00   sampleproject-0.1.0.dist-info/METADATA
       90  2016-01-01 00:00   sampleproject-0.1.0.dist-info/WHEEL
       55  2016-01-01 00:00   sampleproject-0.1.0.dist-info/entry_points.txt
     1081  2016-01-01 00:00   sampleproject-0.1.0.dist-info/licenses/LICENSE.txt
      654  2016-01-01 00:00   sampleproject-0.1.0.dist-info/RECORD
---------                     -------
     6481                     8 files
```

## フォーマット

3rd Party のフォーマッタを追加して実行する。

```sh
> pdm add -d ruff
```

ソースコードをフォーマットする。

```sh
> pdm run ruff format
2 files reformatted, 3 files left unchanged
```

## リント

3rd Party のリンタを追加して実行する。

```sh
> pdm add -d ruff
```

ソースコードを静的解析する。

```sh
> pdm run ruff check --fix
All checks passed!
```

## テスト

`pytest` をプロジェクトに追加する。

```sh
> pdm add -G dev pytest pytest-cov
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
> pdm run pytest
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
setenv =
    PDM_IGNORE_SAVED_PYTHON="1"
allowlist_externals = pdm
skip_install = true
commands =
    pdm install --with dev
    pytest --cov-append
"""
EOF
```

テストを実行する。

```sh
> tox
py311: commands[0]> pdm install --with dev
INFO: Inside an active virtualenv /root/sampleproject/.tox/py311, reusing it.
Set env var PDM_IGNORE_ACTIVE_VENV to ignore it.
? Resolving packages from lockfile...
Synchronizing working set with resolved packages: 11 to add, 0 to update, 0 to remove
  ? Install pytest-cov 6.0.0 successful
  ? Install peppercorn 0.6 successful
  ? Install packaging 24.2 successful
  ? Install iniconfig 2.0.0 successful
  ? Install check-manifest 0.50 successful
  ? Install pyproject-hooks 1.2.0 successful
  ? Install build 1.2.2.post1 successful
  ? Install pluggy 1.5.0 successful
  ? Install pytest 8.3.4 successful
  ? Install coverage 7.6.12 successful
  ? Install ruff 0.9.7 successful
  ? Install sampleproject 0.1.0 successful

  0:00:02 ? All complete! 11/11
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
py311: OK ? in 4.09 seconds
py312: skipped because could not find python interpreter with spec(s): py312
  py311: OK (4.09=setup[0.17]+cmd[3.55,0.36] seconds)
  py312: SKIP (0.01 seconds)
  congratulations :) (4.14 seconds)
```

PDM が管理するインタプリタを使用できないか？
