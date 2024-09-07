# hatch

複数の環境を管理できるプロジェクト管理ツールである。
パッケージ管理、仮想環境管理、タスクランナ機能がある。

- ビルドシステム: `hatchling`
- パッケージマネージャ:  `uv`
- フォーマッタ・リンタ: `ruff`
- テスト: `pytest`, `coverage`

依存関係を操作する機能、ロックファイルの機能はない。

## インストール

スタンドアロンバイナリでインストールする。
初回実行時にダウンロードされる。

```sh
> curl -sSL -o hatch.tar.gz https://github.com/pypa/hatch/releases/latest/download/hatch-x86_64-unknown-linux-gnu.tar.gz
> mkdir -p $HOME/.local/bin
> tar zxf hatch.tar.gz -C $HOME/.local/bin
> chmod 755 $HOME/.local/bin/hatch
> hatch -h
●・・ Unpacking distribution (tar|gzip)
・●・ Unpacking distribution (tar|gzip)
・・● Unpacking distribution (tar|gzip)
・・・ Unpacking distribution (tar|gzip)
●・・ Unpacking distribution (tar|gzip)
・●・ Unpacking distribution (tar|gzip)
・・● Unpacking distribution (tar|gzip)
・・・ Unpacking distribution (tar|gzip)
●・・ Unpacking distribution (tar|gzip)
・●・ Unpacking distribution (tar|gzip)
・・● Unpacking distribution (tar|gzip)
・・・ Unpacking distribution (tar|gzip)
●・・ Unpacking distribution (tar|gzip)
・●・ Unpacking distribution (tar|gzip)
Usage: hatch [OPTIONS] COMMAND [ARGS]...

   _   _       _       _
  | | | |     | |     | |
  | |_| | __ _| |_ ___| |__
  |  _  |/ _` | __/ __| '_ \
  | | | | (_| | || (__| | | |
  \_| |_/\__,_|\__\___|_| |_|

Options:
  -e, --env TEXT                  The name of the environment to use [env var: `HATCH_ENV`]
  -p, --project TEXT              The name of the project to work on [env var: `HATCH_PROJECT`]
  -v, --verbose                   Increase verbosity (can be used additively) [env var: `HATCH_VERBOSE`]
  -q, --quiet                     Decrease verbosity (can be used additively) [env var: `HATCH_QUIET`]
  --color / --no-color            Whether or not to display colored output (default is auto-detection) [env vars:
                                  `FORCE_COLOR`/`NO_COLOR`]
  --interactive / --no-interactive
                                  Whether or not to allow features like prompts and progress bars (default is auto-
                                  detection) [env var: `HATCH_INTERACTIVE`]
  --data-dir TEXT                 The path to a custom directory used to persist data [env var: `HATCH_DATA_DIR`]
  --cache-dir TEXT                The path to a custom directory used to cache data [env var: `HATCH_CACHE_DIR`]
  --config TEXT                   The path to a custom config file to use [env var: `HATCH_CONFIG`]
  --version                       Show the version and exit.
  -h, --help                      Show this message and exit.

Commands:
  build    Build a project
  clean    Remove build artifacts
  config   Manage the config file
  dep      Manage environment dependencies
  env      Manage project environments
  fmt      Format and lint source code
  new      Create or initialize a project
  project  View project information
  publish  Publish build artifacts
  python   Manage Python installations
  run      Run commands within project environments
  self     Manage Hatch
  shell    Enter a shell within a project's environment
  status   Show information about the current environment
  test     Run tests
  version  View or set a project's version
```

## バージョン

```sh
> hatch --version
Hatch, version 1.12.0
```

## プロジェクトの作成

プロジェクトを作成する。

```sh
> hatch new <prpject name>
```

例: [sampleproject](https://github.com/pypa/sampleproject) を管理下にする。

```sh
> hatch new --init .
Description []: A sample Python project

Updated: pyproject.toml
```

以下の設定が更新される。

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
dynamic = ["version"]

[tool.hatch]
```

## 環境の作成

プロジェクトの環境を作成する。

```sh
> hatch env create
```

環境に入る。

```sh
> hatch shell
You are about to enter a new shell, exit as you usually would e.g. by typing `exit` or pressing `ctrl+d`...
source "/root/.local/share/hatch/env/virtual/sampleproject/j8zsfXOY/sampleproject/bin/activate"
(sampleproject) >
```

`sampleproject` が editable モードでインストールされた仮想環境が作成される。

```sh
(sampleproject) > uv pip list
Package       Version Editable project location
------------- ------- -------------------------
peppercorn    0.6
pip           24.0
sampleproject 3.0.0   /tmp/sampleproject
```

環境内でコマンドを実行する。

```sh
(sampleproject) > hatch run uv pip show sampleproject
Name: sampleproject
Version: 3.0.0
Location: /root/.local/share/hatch/env/virtual/sampleproject/j8zsfXOY/sampleproject/lib/python3.12/site-packages
Editable project location: /tmp/sampleproject
Requires: peppercorn
Required-by:
```

## ビルド

パッケージを生成する。

```sh
> hatch build
dist/sampleproject-3.0.0.tar.gz
dist/sampleproject-3.0.0-py3-none-any.whl
```

sdist に含まれるファイルを確認する。

```sh
> tar -tf dist/sampleproject-3.0.0.tar.gz
sampleproject-3.0.0/noxfile.py
sampleproject-3.0.0/.github/workflows/release.yml
sampleproject-3.0.0/.github/workflows/test.yml
sampleproject-3.0.0/src/sample/__init__.py
sampleproject-3.0.0/src/sample/package_data.dat
sampleproject-3.0.0/src/sample/simple.py
sampleproject-3.0.0/tests/__init__.py
sampleproject-3.0.0/tests/test_simple.py
sampleproject-3.0.0/.gitignore
sampleproject-3.0.0/LICENSE.txt
sampleproject-3.0.0/README.md
sampleproject-3.0.0/pyproject.toml
sampleproject-3.0.0/PKG-INFO
```

wheel に含まれるファイルを確認する。

```sh
> unzip -l dist/sampleproject-3.0.0-py3-none-any.whl
Archive:  dist/sampleproject-3.0.0-py3-none-any.whl
  Length      Date    Time    Name
---------  ---------- -----   ----
      111  02-02-2020 00:00   sample/__init__.py
        9  02-02-2020 00:00   sample/package_data.dat
       43  02-02-2020 00:00   sample/simple.py
     4382  02-02-2020 00:00   sampleproject-3.0.0.dist-info/METADATA
       87  02-02-2020 00:00   sampleproject-3.0.0.dist-info/WHEEL
       39  02-02-2020 00:00   sampleproject-3.0.0.dist-info/entry_points.txt
     1081  02-02-2020 00:00   sampleproject-3.0.0.dist-info/licenses/LICENSE.txt
      654  02-02-2020 00:00   sampleproject-3.0.0.dist-info/RECORD
---------                     -------
     6406                     8 files
```

## フォーマット

ソースコードをフォーマットする。

```sh
> hatch fmt --formatter
2 files reformatted, 3 files left unchanged
```

## リント

ソースコードを静的解析する。

```sh
> hatch fmt --linter
src/sample/__init__.py:3:5: T201 `print` found
tests/test_simple.py:12:9: PT009 Use a regular `assert` instead of unittest-style `assertEqual`
Found 3 errors (1 fixed, 2 remaining).
```

## テスト

テストを実行する。

```sh
> hatch test
============================================================================ test session starts =============================================================================
platform linux -- Python 3.12.3, pytest-8.3.2, pluggy-1.5.0
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: rerunfailures-14.0, xdist-3.6.1, mock-3.14.0
collected 1 item

tests/test_simple.py .                                                                                                                                                 [100%]

============================================================================= 1 passed in 0.03s ==============================================================================
```

カバレッジを計測する。

```sh
> hatch test --cover
============================================================================ test session starts =============================================================================
platform linux -- Python 3.12.3, pytest-8.3.2, pluggy-1.5.0
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: rerunfailures-14.0, xdist-3.6.1, mock-3.14.0
collected 1 item

tests/test_simple.py .                                                                                                                                                 [100%]

============================================================================= 1 passed in 0.05s ==============================================================================
Combined data file .coverage.9d2bdba3a18a.637.XvVsVMYx
Name                     Stmts   Miss  Cover
--------------------------------------------
src/sample/__init__.py       2      1    50%
src/sample/simple.py         2      0   100%
tests/__init__.py            0      0   100%
tests/test_simple.py         7      1    86%
--------------------------------------------
TOTAL                       11      2    82%
```

複数のバージョンでテストを実行する。

*pyproject.toml* に下記を追記する。指定しない場合は 3.8 ～ 3.12 で実行される。

```toml
[[tool.hatch.envs.hatch-test.matrix]]
python = ["3.11", "3.12"]
```

すべての matrix を実行する。

```sh
> hatch test -a
============================================================================ test session starts =============================================================================
platform linux -- Python 3.11.9, pytest-8.3.2, pluggy-1.5.0
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: mock-3.14.0, rerunfailures-14.0, xdist-3.6.1
collected 1 item

tests/test_simple.py .                                                                                                                                                 [100%]

============================================================================= 1 passed in 0.02s ==============================================================================
============================================================================ test session starts =============================================================================
platform linux -- Python 3.12.3, pytest-8.3.2, pluggy-1.5.0
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: rerunfailures-14.0, xdist-3.6.1, mock-3.14.0
collected 1 item

tests/test_simple.py .                                                                                                                                                 [100%]

============================================================================= 1 passed in 0.03s ==============================================================================
```

## トラブルシュート

### `hatch env create` が失敗する 1

環境の作成が下記のエラーが発生して失敗する。

```
ValueError: Metadata field `version` cannot be both statically defined and listed in field `project.dynamic`
```

*pyproject.toml* から `project.version` または `project.dynamic` の `version` を削除する。

### `hatch env create` が失敗する 2

環境の作成が下記のエラーが発生して失敗する。

```
ValueError: Missing `tool.hatch.version` configuration
```

*pyproject.toml* で `project.dynamic` に `version` がある場合は `tool.hatch.version`　を設定する。

### `hatch env create` が失敗する 3

環境の作成が下記のエラーが発生して失敗する。

```
The most likely cause of this is that there is no directory that matches the name of your project (sampleproject).
```

プロジェクト名と同じソースディレクトリがない場合は `tool.hatch.build.targets.wheel.packages` を設定する。

```toml
[tool.hatch.build.targets.wheel]
packages = ["src/sample"]
```
