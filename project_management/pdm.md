# PDM

パッケージ管理、依存関係管理、仮想環境管理、タスクランナ機能があるプロジェクト管理ツールである。
フォーマッタ、リンタ、テストはタスクランナ機能を利用する。

## インストール

インストールスクリプトを実行する。

```sh
> curl -sSL https://pdm-project.org/install-pdm.py | python3 -
Installing PDM (2.18.1): Creating virtual environment
Installing PDM (2.18.1): Installing PDM and dependencies
Installing PDM (2.18.1): Making binary at /root/.local/bin
Usage: pdm [-h] [-V] [-c CONFIG] [-v | -q] [--no-cache] [-I] [--pep582 [SHELL]] [-n] ...

    ____  ____  __  ___
   / __ \/ __ \/  |/  /
  / /_/ / / / / /|_/ /
 / ____/ /_/ / /  / /
/_/   /_____/_/  /_/

Options:
  -h, --help            Show this help message and exit.
  -V, --version         Show the version and exit
  -c CONFIG, --config CONFIG
                        Specify another config file path [env var: PDM_CONFIG_FILE]
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
  init                  Initialize a pyproject.toml for PDM
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

Successfully installed: PDM (2.18.1) at /root/.local/bin/pdm
```

## バージョン

```sh
> pdm --version
PDM, version 2.18.1
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
 0. cpython@3.9 (/usr/bin/python3)
 1. cpython@3.9 (/usr/bin/python3.9)
 2. cpython@3.9 (/root/.local/share/pdm/venv/bin/python)
Please select (0):
Virtualenv is created successfully at /tmp/sampleproject/.venv
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
Python requires('*' to allow any) (>=3.9):
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

```
/tmp/sampleproject/.venv/bin/python
```

プロジェクト名と同じ名前のソースコードディレクトリが作成されるため削除する。

```sh
> rm -rf src/sampleproject
```

## 環境の作成

環境に入る。

```sh
> $(pdm venv activate)
(sampleproject-3.9) >
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
INFO: Adding group dev to lockfile
Adding packages to dev dev-dependencies: ruff
  0:00:06 ? Lock successful.
Changes are written to pyproject.toml.
Synchronizing working set with resolved packages: 9 to add, 0 to update, 0 to remove

  ? Install setuptools 74.1.2 successful
  ? Install build 1.2.2 successful
  ? Install check-manifest 0.49 successful
  ? Install pyproject-hooks 1.1.0 successful
  ? Install packaging 24.1 successful
  ? Install importlib-metadata 8.4.0 successful
  ? Install tomli 2.0.1 successful
  ? Install zipp 3.20.1 successful
  ? Install ruff 0.6.4 successful

  0:00:04 ? All complete! 9/9
```

*pyproject.tml* に下記が追加される。

```toml
[project.optional-dependencies]
dev = [
    "ruff>=0.6.4",
]
```

グループ testing にパッケージを追加する。

```sh
> pdm add -d -G testing pytest coverage
INFO: Adding group testing to lockfile
Adding packages to testing dev-dependencies: pytest, coverage
  0:00:04 ? Lock successful.
Changes are written to pyproject.toml.
Synchronizing working set with resolved packages: 7 to add, 0 to update, 0 to remove

  ? Install iniconfig 2.0.0 successful
  ? Install exceptiongroup 1.2.2 successful
  ? Install pluggy 1.5.0 successful
  ? Install packaging 24.1 successful
  ? Install tomli 2.0.1 successful
  ? Install pytest 8.3.2 successful
  ? Install coverage 7.6.1 successful

  0:00:02 ? All complete! 7/7
```

*pyproject.tml* に下記が追加される。

```toml
[tool.pdm.dev-dependencies]
testing = [
    "pytest>=8.3.2",
    "coverage>=7.6.1",
]
```

*pdm.lock* が作成される。

## ビルド

パッケージを生成する。

```sh
> pdm build
Building sdist...
Built sdist at /tmp/sampleproject/dist/sampleproject-3.0.0.tar.gz
Building wheel from sdist...
/usr/lib64/python3.9/tarfile.py:2239: RuntimeWarning: The default behavior of tarfile extraction has been changed to disallow common exploits (including CVE-2007-4559). By default, absolute/parent paths are disallowed and some mode bits are cleared. See https://access.redhat.com/articles/7004769 for more details.
  warnings.warn(
Built wheel at /tmp/sampleproject/dist/sampleproject-3.0.0-py3-none-any.whl
```

sdist に含まれるファイルを確認する。

```sh
> tar -tf dist/sampleproject-3.0.0.tar.gz
sampleproject-3.0.0/LICENSE.txt
sampleproject-3.0.0/README.md
sampleproject-3.0.0/pyproject.toml
sampleproject-3.0.0/src/sample/__init__.py
sampleproject-3.0.0/src/sample/package_data.dat
sampleproject-3.0.0/src/sample/simple.py
sampleproject-3.0.0/tests/__init__.py
sampleproject-3.0.0/tests/test_simple.py
sampleproject-3.0.0/PKG-INFO
```

wheel に含まれるファイルを確認する。

```sh
> unzip -l dist/sampleproject-3.0.0-py3-none-any.whl
Archive:  dist/sampleproject-3.0.0-py3-none-any.whl
  Length      Date    Time    Name
---------  ---------- -----   ----
      111  01-01-2016 00:00   sample/__init__.py
        9  01-01-2016 00:00   sample/package_data.dat
       43  01-01-2016 00:00   sample/simple.py
     4356  01-01-2016 00:00   sampleproject-3.0.0.dist-info/METADATA
       90  01-01-2016 00:00   sampleproject-3.0.0.dist-info/WHEEL
       40  01-01-2016 00:00   sampleproject-3.0.0.dist-info/entry_points.txt
     1081  01-01-2016 00:00   sampleproject-3.0.0.dist-info/licenses/LICENSE.txt
      654  01-01-2016 00:00   sampleproject-3.0.0.dist-info/RECORD
---------                     -------
     6384                     8 files
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
======================================================================== test session starts =========================================================================
platform linux -- Python 3.11.7, pytest-8.3.2, pluggy-1.5.0 -- /tmp/sampleproject/.venv/bin/python
cachedir: .pytest_cache
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: cov-5.0.0
collected 1 item

tests/test_simple.py::TestSimple::test_add_one PASSED                                                                                                          [100%]

---------- coverage: platform linux, python 3.11.7-final-0 -----------
Name                     Stmts   Miss  Cover
--------------------------------------------
src/sample/__init__.py       2      1    50%
src/sample/simple.py         2      0   100%
--------------------------------------------
TOTAL                        4      1    75%


========================================================================= 1 passed in 0.05s ==========================================================================
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
deps = pdm
commands =
    pdm install -G dev
    pytest --cov-append
"""
EOF
```

テストを実行する。

```sh
> tox
.pkg: _optional_hooks> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True pdm.backend
.pkg: get_requires_for_build_sdist> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True pdm.backend
.pkg: build_sdist> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True pdm.backend
py311: install_package> python -I -m pip install --force-reinstall --no-deps /tmp/sampleproject/.tox/.tmp/package/9/sampleproject-3.0.0.tar.gz
py311: commands[0]> pdm install -G dev
INFO: Inside an active virtualenv /tmp/sampleproject/.tox/py311, reusing it.
Set env var PDM_IGNORE_ACTIVE_VENV to ignore it.
Synchronizing working set with resolved packages: 7 to add, 0 to update, 0 to remove

  ? Install check-manifest 0.49 successful
    Installing check-manifest 0.49... Downloading... 100%
  ? Install iniconfig 2.0.0 successful
  ? Install pytest 8.3.2 successful
  ? Install coverage 7.6.1 successful
  ? Install ruff 0.6.4 successful
  ? Update sampleproject 3.0.0 -> 3.0.0 successful

  0:00:03 ? All complete! 7/7
py311: commands[1]> pytest --cov-append
========================================================================== test session starts ==========================================================================
platform linux -- Python 3.11.7, pytest-8.3.2, pluggy-1.5.0
cachedir: .tox/py311/.pytest_cache
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: anyio-4.4.0, cov-5.0.0
collected 1 item

tests/test_simple.py .                                                                                                                                            [100%]

=========================================================================== 1 passed in 0.05s ===========================================================================
py311: OK ? in 8.07 seconds
py312: install_package> python -I -m pip install --force-reinstall --no-deps /tmp/sampleproject/.tox/.tmp/package/10/sampleproject-3.0.0.tar.gz
py312: commands[0]> pdm install -G dev
INFO: Inside an active virtualenv /tmp/sampleproject/.tox/py312, reusing it.
Set env var PDM_IGNORE_ACTIVE_VENV to ignore it.
Synchronizing working set with resolved packages: 8 to add, 0 to update, 0 to remove

  ? Install setuptools 74.1.2 successful
  ? Install iniconfig 2.0.0 successful
  ? Install check-manifest 0.49 successful
  ? Install pytest-cov 5.0.0 successful
  ? Install pytest 8.3.2 successful
  ? Install coverage 7.6.1 successful
  ? Install ruff 0.6.4 successful
  ? Update sampleproject 3.0.0 -> 3.0.0 successful

  0:00:04 ? All complete! 8/8
py312: commands[1]> pytest --cov-append
========================================================================== test session starts ==========================================================================
platform linux -- Python 3.12.1, pytest-8.3.2, pluggy-1.5.0
cachedir: .tox/py312/.pytest_cache
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: anyio-4.4.0, cov-5.0.0
collected 1 item

tests/test_simple.py .                                                                                                                                            [100%]

=========================================================================== 1 passed in 0.02s ===========================================================================
.pkg: _exit> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True pdm.backend
  py311: OK (8.07=setup[1.85]+cmd[5.28,0.94] seconds)
  py312: OK (9.45=setup[2.05]+cmd[6.62,0.78] seconds)
  congratulations :) (17.59 seconds)
```

PDM が管理するインタプリタを使用できないか？
