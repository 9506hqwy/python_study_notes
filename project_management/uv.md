# uv

パッケージ管理、依存関係管理、タスクランナ機能があるプロジェクト管理ツールである。
フォーマッタ、リンタ、テストはタスクランナ機能を利用する。

複数の仮想環境を操作する機能はないが、
コマンドの実行にインタプリタを指定すると仮想環境を都度再作成し複数のインタプリタに対応する。

ツールを専用の仮想環境にインストールする機能がある。

## インストール

[](../package_management/uv.md) を参照する。

## バージョン

```sh
> uv --version
uv 0.6.2
```

## プロジェクトの作成

プロジェクトを作成する。

```sh
> uv init
```

例: [sampleproject](https://github.com/pypa/sampleproject) を管理下にする。

`uv init` は実行できない。

```sh
> uv sync
Using CPython 3.13.2 interpreter at: /usr/local/bin/python3
Creating virtual environment at: .venv
Resolved 12 packages in 531ms
      Built sampleproject @ file:///root/sampleproject
Prepared 2 packages in 1.15s
Installed 2 packages in 2ms
 + peppercorn==0.6
 + sampleproject==4.0.0 (from file:///root/sampleproject)
```

`sampleproject` が editable モードでインストールされた仮想環境が作成される。

```sh
> uv pip list
Package       Version Editable project location
------------- ------- -------------------------
peppercorn    0.6
sampleproject 4.0.0   /root/sampleproject
```

*uv.lock* が作成される。

## 環境の作成

環境に入る。

```sh
> source .venv/bin/activate
(sampleproject) >
```

インタプリタをインストールする。
`uv python dir` にインストールされる。

```sh
> uv python install 3.12
Installed Python 3.12.9 in 3.89s
 + cpython-3.12.9-linux-x86_64-gnu
```

## 依存関係

任意のグループを指定して依存関係を追加することはできる。

`--dev` は `dependency-groups` に追加される。
`--optional <GROUP>` は `project.optional-dependecies.<GROUP>` に追加される。

*uv.lock* 内でグループを区別するため、グループを選択的にインストールすることができる。

### オプショナルなパッケージを追加

グループ dev にパッケージを追加する。

```sh
> uv add --dev pytest
Resolved 16 packages in 218ms
      Built sampleproject @ file:///root/sampleproject
Prepared 5 packages in 604ms
Uninstalled 1 package in 0.38ms
Installed 5 packages in 2ms
 + iniconfig==2.0.0
 + packaging==24.2
 + pluggy==1.5.0
 + pytest==8.3.4
 ~ sampleproject==4.0.0 (from file:///root/sampleproject)
```

*pyproject.tml* に下記が追加される。

```toml
[dependency-groups]
dev = [
    "pytest>=8.3.4",
]
```

グループ lint にパッケージを追加する。

```sh
> uv add --optional lint ruff
Resolved 17 packages in 269ms
      Built sampleproject @ file:///root/sampleproject
Prepared 2 packages in 1.37s
Uninstalled 1 package in 0.42ms
Installed 2 packages in 2ms
 + ruff==0.9.7
 ~ sampleproject==4.0.0 (from file:///root/sampleproject)
```

*pyproject.tml* に下記が追加される。

```toml
[project.optional-dependencies]
lint = [
    "ruff>=0.9.7",
]
```

*uv.lock* が更新される。

### ローカルファイルのパッケージを追加

*pyproject.toml* に相対パスでローカルにあるパッケージを指定する。

プロジェクトを作成する。

```sh
> uv init --name sample2 --lib --vcs git sampleproject2
Initialized project `sample2` at `/root/sampleproject2`
```

PyPI の依存関係を追加する。

```sh
> uv add peppercorn
Using CPython 3.12.9
Creating virtual environment at: .venv
Resolved 2 packages in 75ms
      Built sample2 @ file:///root/sampleproject2
Prepared 1 package in 610ms
Installed 2 packages in 1ms
 + peppercorn==0.6
 + sample2==0.1.0 (from file:///root/sampleproject2)
```

ローカルファイル *lib/sampleproject-4.0.0-py3-none-any.whl* の依存関係を追加する。

```sh
> uv add ./lib/sampleproject-4.0.0-py3-none-any.whl
Resolved 3 packages in 4ms
      Built sample2 @ file:///root/sampleproject2
Prepared 1 package in 212ms
Uninstalled 1 package in 0.26ms
Installed 2 packages in 1ms
 ~ sample2==0.1.0 (from file:///root/sampleproject2)
 + sampleproject==4.0.0 (from file:///root/sampleproject2/lib/sampleproject-4.0.0-py3-none-any.whl)
```

*pyproject.toml* に下記が追加される。

```toml
[project]
dependencies = [
    "sampleproject",
]

[tool.uv.sources]
sampleproject = { path = "lib/sampleproject-4.0.0-py3-none-any.whl" }
```

## ビルド

パッケージを生成する。

```sh
> uv build
Building source distribution...
running egg_info
writing src/sampleproject.egg-info/PKG-INFO
writing dependency_links to src/sampleproject.egg-info/dependency_links.txt
writing entry points to src/sampleproject.egg-info/entry_points.txt
writing requirements to src/sampleproject.egg-info/requires.txt
writing top-level names to src/sampleproject.egg-info/top_level.txt
reading manifest file 'src/sampleproject.egg-info/SOURCES.txt'
adding license file 'LICENSE.txt'
writing manifest file 'src/sampleproject.egg-info/SOURCES.txt'
running sdist
running egg_info
writing src/sampleproject.egg-info/PKG-INFO
writing dependency_links to src/sampleproject.egg-info/dependency_links.txt
writing entry points to src/sampleproject.egg-info/entry_points.txt
writing requirements to src/sampleproject.egg-info/requires.txt
writing top-level names to src/sampleproject.egg-info/top_level.txt
reading manifest file 'src/sampleproject.egg-info/SOURCES.txt'
adding license file 'LICENSE.txt'
writing manifest file 'src/sampleproject.egg-info/SOURCES.txt'
running check
creating sampleproject-4.0.0
creating sampleproject-4.0.0/src/sample
creating sampleproject-4.0.0/src/sampleproject.egg-info
creating sampleproject-4.0.0/tests
copying files to sampleproject-4.0.0...
copying LICENSE.txt -> sampleproject-4.0.0
copying README.md -> sampleproject-4.0.0
copying pyproject.toml -> sampleproject-4.0.0
copying src/sample/__init__.py -> sampleproject-4.0.0/src/sample
copying src/sample/package_data.dat -> sampleproject-4.0.0/src/sample
copying src/sample/simple.py -> sampleproject-4.0.0/src/sample
copying src/sampleproject.egg-info/PKG-INFO -> sampleproject-4.0.0/src/sampleproject.egg-info
copying src/sampleproject.egg-info/SOURCES.txt -> sampleproject-4.0.0/src/sampleproject.egg-info
copying src/sampleproject.egg-info/dependency_links.txt -> sampleproject-4.0.0/src/sampleproject.egg-info
copying src/sampleproject.egg-info/entry_points.txt -> sampleproject-4.0.0/src/sampleproject.egg-info
copying src/sampleproject.egg-info/requires.txt -> sampleproject-4.0.0/src/sampleproject.egg-info
copying src/sampleproject.egg-info/top_level.txt -> sampleproject-4.0.0/src/sampleproject.egg-info
copying tests/test_simple.py -> sampleproject-4.0.0/tests
copying src/sampleproject.egg-info/SOURCES.txt -> sampleproject-4.0.0/src/sampleproject.egg-info
Writing sampleproject-4.0.0/setup.cfg
Creating tar archive
removing 'sampleproject-4.0.0' (and everything under it)
Building wheel from source distribution...
running egg_info
writing src/sampleproject.egg-info/PKG-INFO
writing dependency_links to src/sampleproject.egg-info/dependency_links.txt
writing entry points to src/sampleproject.egg-info/entry_points.txt
writing requirements to src/sampleproject.egg-info/requires.txt
writing top-level names to src/sampleproject.egg-info/top_level.txt
reading manifest file 'src/sampleproject.egg-info/SOURCES.txt'
adding license file 'LICENSE.txt'
writing manifest file 'src/sampleproject.egg-info/SOURCES.txt'
running bdist_wheel
running build
running build_py
creating build/lib/sample
copying src/sample/__init__.py -> build/lib/sample
copying src/sample/simple.py -> build/lib/sample
running egg_info
writing src/sampleproject.egg-info/PKG-INFO
writing dependency_links to src/sampleproject.egg-info/dependency_links.txt
writing entry points to src/sampleproject.egg-info/entry_points.txt
writing requirements to src/sampleproject.egg-info/requires.txt
writing top-level names to src/sampleproject.egg-info/top_level.txt
reading manifest file 'src/sampleproject.egg-info/SOURCES.txt'
adding license file 'LICENSE.txt'
writing manifest file 'src/sampleproject.egg-info/SOURCES.txt'
copying src/sample/package_data.dat -> build/lib/sample
installing to build/bdist.linux-x86_64/wheel
running install
running install_lib
creating build/bdist.linux-x86_64/wheel
creating build/bdist.linux-x86_64/wheel/sample
copying build/lib/sample/__init__.py -> build/bdist.linux-x86_64/wheel/./sample
copying build/lib/sample/simple.py -> build/bdist.linux-x86_64/wheel/./sample
copying build/lib/sample/package_data.dat -> build/bdist.linux-x86_64/wheel/./sample
running install_egg_info
Copying src/sampleproject.egg-info to build/bdist.linux-x86_64/wheel/./sampleproject-4.0.0-py3.13.egg-info
running install_scripts
creating build/bdist.linux-x86_64/wheel/sampleproject-4.0.0.dist-info/WHEEL
creating '/root/sampleproject/dist/.tmp-7jx73igm/sampleproject-4.0.0-py3-none-any.whl' and adding 'build/bdist.linux-x86_64/wheel' to it
adding 'sample/__init__.py'
adding 'sample/package_data.dat'
adding 'sample/simple.py'
adding 'sampleproject-4.0.0.dist-info/LICENSE.txt'
adding 'sampleproject-4.0.0.dist-info/METADATA'
adding 'sampleproject-4.0.0.dist-info/WHEEL'
adding 'sampleproject-4.0.0.dist-info/entry_points.txt'
adding 'sampleproject-4.0.0.dist-info/top_level.txt'
adding 'sampleproject-4.0.0.dist-info/RECORD'
removing build/bdist.linux-x86_64/wheel
Successfully built dist/sampleproject-4.0.0.tar.gz
Successfully built dist/sampleproject-4.0.0-py3-none-any.whl
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
      111  2025-02-22 07:59   sample/__init__.py
        9  2025-02-22 07:59   sample/package_data.dat
       43  2025-02-22 07:59   sample/simple.py
     1081  2025-02-22 07:59   sampleproject-4.0.0.dist-info/LICENSE.txt
     4457  2025-02-22 07:59   sampleproject-4.0.0.dist-info/METADATA
       91  2025-02-22 07:59   sampleproject-4.0.0.dist-info/WHEEL
       39  2025-02-22 07:59   sampleproject-4.0.0.dist-info/entry_points.txt
        7  2025-02-22 07:59   sampleproject-4.0.0.dist-info/top_level.txt
      742  2025-02-22 07:59   sampleproject-4.0.0.dist-info/RECORD
---------                     -------
     6580                     9 files
```

## フォーマット

3rd Party のフォーマッタを追加して実行する。

```sh
> uv tool install ruff
Resolved 1 package in 84ms
Installed 1 package in 5ms
 + ruff==0.9.7
Installed 1 executable: ruff
```

ソースコードをフォーマットする。

```sh
> uv tool run ruff format
2 files reformatted, 3 files left unchanged
```

## リント

3rd Party のフォーマッタを追加して実行する。

```sh
> uv tool install ruff
Resolved 1 package in 7ms
Installed 1 package in 2ms
 + ruff==0.6.4
Installed 1 executable: ruff
```

ソースコードを静的解析する。

```sh
> uv tool run ruff check --fix
All checks passed!
```

## テスト

`pytest` をプロジェクトに追加する。

```sh
> uv add --dev pytest pytest-cov
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
> uv run pytest
      Built sampleproject @ file:///root/sampleproject
Uninstalled 1 package in 0.29ms
Installed 1 package in 2ms
========================================================================================= test session starts =========================================================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0 -- /root/sampleproject/.venv/bin/python3
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
    UV_PROJECT_ENVIRONMENT=.tox/{env_name}
allowlist_externals = uv
skip_install = true
commands =
    uv sync
    pytest --cov-append
"""
EOF
```

テストを実行する。

```sh
> tox
py311: commands[0]> uv sync
Resolved 18 packages in 0.73ms
      Built sampleproject @ file:///root/sampleproject
Prepared 2 packages in 522ms
Installed 8 packages in 3ms
 + coverage==7.6.12
 + iniconfig==2.0.0
 + packaging==24.2
 + peppercorn==0.6
 + pluggy==1.5.0
 + pytest==8.3.4
 + pytest-cov==6.0.0
 + sampleproject==4.0.0 (from file:///root/sampleproject)
py311: commands[1]> pytest --cov-append
========================================================================================= test session starts =========================================================================================
platform linux -- Python 3.11.2, pytest-8.3.4, pluggy-1.5.0 -- /root/sampleproject/.tox/py311/bin/python3
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
py311: OK ? in 1.3 seconds
py312: skipped because could not find python interpreter with spec(s): py312
  py311: OK (1.30=setup[0.37]+cmd[0.57,0.36] seconds)
  py312: SKIP (0.04 seconds)
  congratulations :) (1.39 seconds)
```

uv が管理するインタプリタを使用する。システムに tox-uv をインストールする。

```sh
> pip3 install tox tox-uv
```

システムの tox を実行する。

```sh
> tox
py311: recreate env because env type changed from {'name': 'py311', 'type': 'VirtualEnvRunner'} to {'name': 'py311', 'type': 'UvVenvRunner'}
py311: remove tox env folder /root/sampleproject/.tox/py311
py311: venv> /usr/local/bin/uv venv -p cpython3.11 --allow-existing --python-preference system /root/sampleproject/.tox/py311
py311: commands[0]> uv sync
Resolved 18 packages in 0.53ms
      Built sampleproject @ file:///root/sampleproject
Prepared 1 package in 524ms
Installed 8 packages in 2ms
 + coverage==7.6.12
 + iniconfig==2.0.0
 + packaging==24.2
 + peppercorn==0.6
 + pluggy==1.5.0
 + pytest==8.3.4
 + pytest-cov==6.0.0
 + sampleproject==4.0.0 (from file:///root/sampleproject)
py311: commands[1]> pytest --cov-append
========================================================================================= test session starts =========================================================================================
platform linux -- Python 3.11.2, pytest-8.3.4, pluggy-1.5.0 -- /root/sampleproject/.tox/py311/bin/python3
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
py311: OK ? in 0.96 seconds
py312: recreate env because env type changed from {'name': 'py312', 'type': 'VirtualEnvRunner'} to {'name': 'py312', 'type': 'UvVenvRunner'}
py312: remove tox env folder /root/sampleproject/.tox/py312
py312: venv> /usr/local/bin/uv venv -p cpython3.12 --allow-existing --python-preference system /root/sampleproject/.tox/py312
py312: commands[0]> uv sync
Resolved 18 packages in 0.68ms
Prepared 1 package in 136ms
Installed 8 packages in 8ms
 + coverage==7.6.12
 + iniconfig==2.0.0
 + packaging==24.2
 + peppercorn==0.6
 + pluggy==1.5.0
 + pytest==8.3.4
 + pytest-cov==6.0.0
 + sampleproject==4.0.0 (from file:///root/sampleproject)
py312: commands[1]> pytest --cov-append
========================================================================================= test session starts =========================================================================================
platform linux -- Python 3.12.9, pytest-8.3.4, pluggy-1.5.0 -- /root/sampleproject/.tox/py312/bin/python3
cachedir: .tox/py312/.pytest_cache
rootdir: /root/sampleproject
configfile: pyproject.toml
plugins: cov-6.0.0
collected 1 item

tests/test_simple.py::TestSimple::test_add_one PASSED                                                                                                                                           [100%]

---------- coverage: platform linux, python 3.12.9-final-0 -----------
Name                     Stmts   Miss  Cover
--------------------------------------------
src/sample/__init__.py       2      1    50%
src/sample/simple.py         2      0   100%
--------------------------------------------
TOTAL                        4      1    75%


========================================================================================== 1 passed in 0.04s ==========================================================================================
  py311: OK (0.96=setup[0.06]+cmd[0.54,0.36] seconds)
  py312: OK (1.02=setup[0.02]+cmd[0.27,0.74] seconds)
  congratulations :) (2.03 seconds)
```
