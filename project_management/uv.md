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
uv 0.4.7
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
Using Python 3.9.18 interpreter at: /usr/bin/python3
Creating virtualenv at: .venv
Resolved 12 packages in 592ms
   Built sampleproject @ file:///tmp/sampleproject
Prepared 2 packages in 2.06s
Installed 2 packages in 27ms
 + peppercorn==0.6
 + sampleproject==3.0.0 (from file:///tmp/sampleproject)
```

`sampleproject` が editable モードでインストールされた仮想環境が作成される。

```sh
> uv pip list
Package       Version Editable project location
------------- ------- -------------------------
peppercorn    0.6
sampleproject 3.0.0   /tmp/sampleproject
```

## 環境の作成

環境に入る。

```sh
> source .venv/bin/activate
(sampleproject) >
```

## 依存関係

任意のグループを指定して依存関係を追加することはできない。

`--dev` はツール独自の `tool.uv.dev-dependencies` に追加される。
`--optional <GROUP>` は `project.optional-dependecies.<GROUP>` に追加される。

*uv.lock* 内でグループを区別するため、グループを選択的にインストールすることができる。

### オプショナルなパッケージを追加

グループ dev にパッケージを追加する。

```sh
> uv add --dev pytest
Resolved 16 packages in 385ms
   Built sampleproject @ file:///tmp/sampleproject
Prepared 8 packages in 1.36s
Installed 8 packages in 36ms
 + exceptiongroup==1.2.2
 + iniconfig==2.0.0
 + packaging==24.1
 + peppercorn==0.6
 + pluggy==1.5.0
 + pytest==8.3.2
 + sampleproject==3.0.0 (from file:///tmp/sampleproject)
 + tomli==2.0.1
```

*pyproject.tml* に下記が追加される。

```toml
[tool.uv]
dev-dependencies = [
    "pytest>=8.3.2",
]
```

グループ lint にパッケージを追加する。

```sh
> uv add --optional lint ruff
Resolved 17 packages in 369ms
   Built sampleproject @ file:///tmp/sampleproject
Prepared 2 packages in 1.60s
Uninstalled 1 package in 0.45ms
Installed 2 packages in 10ms
 + ruff==0.6.4
 ~ sampleproject==3.0.0 (from file:///tmp/sampleproject)
```

*pyproject.tml* に下記が追加される。

```toml
[project.optional-dependencies]
lint = [
    "ruff>=0.6.4",
]
```

*uv.lock* が更新される。

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
creating sampleproject-3.0.0
creating sampleproject-3.0.0/src
creating sampleproject-3.0.0/src/sample
creating sampleproject-3.0.0/src/sampleproject.egg-info
creating sampleproject-3.0.0/tests
copying files to sampleproject-3.0.0...
copying LICENSE.txt -> sampleproject-3.0.0
copying README.md -> sampleproject-3.0.0
copying pyproject.toml -> sampleproject-3.0.0
copying src/sample/__init__.py -> sampleproject-3.0.0/src/sample
copying src/sample/package_data.dat -> sampleproject-3.0.0/src/sample
copying src/sample/simple.py -> sampleproject-3.0.0/src/sample
copying src/sampleproject.egg-info/PKG-INFO -> sampleproject-3.0.0/src/sampleproject.egg-info
copying src/sampleproject.egg-info/SOURCES.txt -> sampleproject-3.0.0/src/sampleproject.egg-info
copying src/sampleproject.egg-info/dependency_links.txt -> sampleproject-3.0.0/src/sampleproject.egg-info
copying src/sampleproject.egg-info/entry_points.txt -> sampleproject-3.0.0/src/sampleproject.egg-info
copying src/sampleproject.egg-info/requires.txt -> sampleproject-3.0.0/src/sampleproject.egg-info
copying src/sampleproject.egg-info/top_level.txt -> sampleproject-3.0.0/src/sampleproject.egg-info
copying tests/test_simple.py -> sampleproject-3.0.0/tests
copying src/sampleproject.egg-info/SOURCES.txt -> sampleproject-3.0.0/src/sampleproject.egg-info
Writing sampleproject-3.0.0/setup.cfg
Creating tar archive
removing 'sampleproject-3.0.0' (and everything under it)
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
creating build
creating build/lib
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
creating build/bdist.linux-x86_64
creating build/bdist.linux-x86_64/wheel
creating build/bdist.linux-x86_64/wheel/sample
copying build/lib/sample/__init__.py -> build/bdist.linux-x86_64/wheel/./sample
copying build/lib/sample/simple.py -> build/bdist.linux-x86_64/wheel/./sample
copying build/lib/sample/package_data.dat -> build/bdist.linux-x86_64/wheel/./sample
running install_egg_info
Copying src/sampleproject.egg-info to build/bdist.linux-x86_64/wheel/./sampleproject-3.0.0-py3.9.egg-info
running install_scripts
creating build/bdist.linux-x86_64/wheel/sampleproject-3.0.0.dist-info/WHEEL
creating '/tmp/sampleproject/dist/.tmpyqVEZg/.tmp-lpxmbhd8/sampleproject-3.0.0-py3-none-any.whl' and adding 'build/bdist.linux-x86_64/wheel' to it
adding 'sample/__init__.py'
adding 'sample/package_data.dat'
adding 'sample/simple.py'
adding 'sampleproject-3.0.0.dist-info/LICENSE.txt'
adding 'sampleproject-3.0.0.dist-info/METADATA'
adding 'sampleproject-3.0.0.dist-info/WHEEL'
adding 'sampleproject-3.0.0.dist-info/entry_points.txt'
adding 'sampleproject-3.0.0.dist-info/top_level.txt'
adding 'sampleproject-3.0.0.dist-info/RECORD'
removing build/bdist.linux-x86_64/wheel
Successfully built dist/sampleproject-3.0.0.tar.gz and dist/sampleproject-3.0.0-py3-none-any.whl
```

sdist に含まれるファイルを確認する。

```sh
> tar -tf dist/sampleproject-3.0.0.tar.gz
sampleproject-3.0.0/
sampleproject-3.0.0/LICENSE.txt
sampleproject-3.0.0/PKG-INFO
sampleproject-3.0.0/README.md
sampleproject-3.0.0/pyproject.toml
sampleproject-3.0.0/setup.cfg
sampleproject-3.0.0/src/
sampleproject-3.0.0/src/sample/
sampleproject-3.0.0/src/sample/__init__.py
sampleproject-3.0.0/src/sample/package_data.dat
sampleproject-3.0.0/src/sample/simple.py
sampleproject-3.0.0/src/sampleproject.egg-info/
sampleproject-3.0.0/src/sampleproject.egg-info/PKG-INFO
sampleproject-3.0.0/src/sampleproject.egg-info/SOURCES.txt
sampleproject-3.0.0/src/sampleproject.egg-info/dependency_links.txt
sampleproject-3.0.0/src/sampleproject.egg-info/entry_points.txt
sampleproject-3.0.0/src/sampleproject.egg-info/requires.txt
sampleproject-3.0.0/src/sampleproject.egg-info/top_level.txt
sampleproject-3.0.0/tests/
sampleproject-3.0.0/tests/test_simple.py
```

wheel に含まれるファイルを確認する。

```sh
> unzip -l dist/sampleproject-3.0.0-py3-none-any.whl
Archive:  dist/sampleproject-3.0.0-py3-none-any.whl
  Length      Date    Time    Name
---------  ---------- -----   ----
      111  09-08-2024 10:17   sample/__init__.py
        9  09-08-2024 10:17   sample/package_data.dat
       43  09-08-2024 10:17   sample/simple.py
     1081  09-08-2024 10:17   sampleproject-3.0.0.dist-info/LICENSE.txt
     4393  09-08-2024 10:17   sampleproject-3.0.0.dist-info/METADATA
       91  09-08-2024 10:17   sampleproject-3.0.0.dist-info/WHEEL
       39  09-08-2024 10:17   sampleproject-3.0.0.dist-info/entry_points.txt
        7  09-08-2024 10:17   sampleproject-3.0.0.dist-info/top_level.txt
      742  09-08-2024 10:17   sampleproject-3.0.0.dist-info/RECORD
---------                     -------
     6516                     9 files
```

## フォーマット

3rd Party のフォーマッタを追加して実行する。

```sh
> uv tool install ruff
Resolved 1 package in 7ms
Installed 1 package in 2ms
 + ruff==0.6.4
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
   Built sampleproject @ file:///tmp/sampleproject
Uninstalled 1 package in 0.46ms
Installed 1 package in 4ms
======================================================================== test session starts =========================================================================
platform linux -- Python 3.9.18, pytest-8.3.2, pluggy-1.5.0 -- /tmp/sampleproject/.venv/bin/python3
cachedir: .pytest_cache
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: cov-5.0.0
collected 1 item

tests/test_simple.py::TestSimple::test_add_one PASSED                                                                                                          [100%]

---------- coverage: platform linux, python 3.9.18-final-0 -----------
Name                     Stmts   Miss  Cover
--------------------------------------------
src/sample/__init__.py       2      1    50%
src/sample/simple.py         2      0   100%
--------------------------------------------
TOTAL                        4      1    75%


========================================================================= 1 passed in 0.06s ==========================================================================
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
deps = uv
commands =
    uv sync
    pytest --cov-append
"""
EOF
```

テストを実行する。

```sh
> tox
.pkg: _optional_hooks> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: get_requires_for_build_sdist> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: build_sdist> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True setuptools.build_meta
py311: install_package> python -I -m pip install --force-reinstall --no-deps /tmp/sampleproject/.tox/.tmp/package/7/sampleproject-3.0.0.tar.gz
py311: commands[0]> uv sync
Resolved 17 packages in 1ms
   Built sampleproject @ file:///tmp/sampleproject
Prepared 7 packages in 919ms
Uninstalled 1 package in 0.54ms
Installed 7 packages in 15ms
 + coverage==7.6.1
 + iniconfig==2.0.0
 + packaging==24.1
 + pluggy==1.5.0
 + pytest==8.3.2
 + pytest-cov==5.0.0
 - sampleproject==3.0.0 (from file:///tmp/sampleproject/.tox/.tmp/package/7/sampleproject-3.0.0.tar.gz)
 + sampleproject==3.0.0 (from file:///tmp/sampleproject)
py311: commands[1]> pytest --cov-append
======================================================================== test session starts =========================================================================
platform linux -- Python 3.11.7, pytest-8.3.2, pluggy-1.5.0 -- /tmp/sampleproject/.tox/py311/bin/python3
cachedir: .tox/py311/.pytest_cache
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


========================================================================= 1 passed in 0.06s ==========================================================================
py311: OK ? in 5.38 seconds
py312: install_package> python -I -m pip install --force-reinstall --no-deps /tmp/sampleproject/.tox/.tmp/package/8/sampleproject-3.0.0.tar.gz
py312: commands[0]> uv sync
Resolved 17 packages in 0.81ms
Prepared 6 packages in 118ms
Uninstalled 1 package in 0.60ms
Installed 7 packages in 14ms
 + coverage==7.6.1
 + iniconfig==2.0.0
 + packaging==24.1
 + pluggy==1.5.0
 + pytest==8.3.2
 + pytest-cov==5.0.0
 - sampleproject==3.0.0 (from file:///tmp/sampleproject/.tox/.tmp/package/8/sampleproject-3.0.0.tar.gz)
 + sampleproject==3.0.0 (from file:///tmp/sampleproject)
py312: commands[1]> pytest --cov-append
======================================================================== test session starts =========================================================================
platform linux -- Python 3.12.1, pytest-8.3.2, pluggy-1.5.0 -- /tmp/sampleproject/.tox/py312/bin/python3
cachedir: .tox/py312/.pytest_cache
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: cov-5.0.0
collected 1 item

tests/test_simple.py::TestSimple::test_add_one PASSED                                                                                                          [100%]

---------- coverage: platform linux, python 3.12.1-final-0 -----------
Name                     Stmts   Miss  Cover
--------------------------------------------
src/sample/__init__.py       2      1    50%
src/sample/simple.py         2      0   100%
--------------------------------------------
TOTAL                        4      1    75%


========================================================================= 1 passed in 0.07s ==========================================================================
  py311: OK (5.38=setup[3.75]+cmd[0.96,0.67] seconds)
  py312: OK (4.12=setup[3.23]+cmd[0.15,0.75] seconds)
  congratulations :) (9.58 seconds)
```

uv が管理するインタプリタを使用する。システムに tox-uv をインストールする。

```sh
> pip3 install tox tox-uv
```

*~/.local/bin/uv* が必要なためシンボリックリンクを作成する。

```sh
> ln -s /root/.cargo/bin/uv uv
```

システムの tox を実行する。

```sh
> tox
.pkg: _optional_hooks> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: get_requires_for_build_sdist> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: build_sdist> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True setuptools.build_meta
py311: install_package> /root/.local/bin/uv pip install --reinstall --no-deps sampleproject@/tmp/sampleproject/.tox/.tmp/package/3/sampleproject-3.0.0.tar.gz
py311: commands[0]> uv sync
Resolved 17 packages in 0.74ms
Uninstalled 1 package in 0.32ms
Installed 1 package in 3ms
 - sampleproject==3.0.0 (from file:///tmp/sampleproject/.tox/.tmp/package/3/sampleproject-3.0.0.tar.gz)
 + sampleproject==3.0.0 (from file:///tmp/sampleproject)
py311: commands[1]> pytest --cov-append
======================================================================= test session starts ========================================================================
platform linux -- Python 3.11.9, pytest-8.3.2, pluggy-1.5.0 -- /tmp/sampleproject/.tox/py311/bin/python3
cachedir: .tox/py311/.pytest_cache
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: cov-5.0.0
collected 1 item

tests/test_simple.py::TestSimple::test_add_one PASSED                                                                                                        [100%]

---------- coverage: platform linux, python 3.11.9-final-0 -----------
Name                     Stmts   Miss  Cover
--------------------------------------------
src/sample/__init__.py       2      1    50%
src/sample/simple.py         2      0   100%
--------------------------------------------
TOTAL                        4      1    75%


======================================================================== 1 passed in 0.05s =========================================================================
py311: OK ? in 2.22 seconds
py312: install_package> /root/.local/bin/uv pip install --reinstall --no-deps sampleproject@/tmp/sampleproject/.tox/.tmp/package/4/sampleproject-3.0.0.tar.gz
py312: commands[0]> uv sync
Resolved 17 packages in 1ms
Uninstalled 1 package in 0.37ms
Installed 1 package in 3ms
 - sampleproject==3.0.0 (from file:///tmp/sampleproject/.tox/.tmp/package/4/sampleproject-3.0.0.tar.gz)
 + sampleproject==3.0.0 (from file:///tmp/sampleproject)
py312: commands[1]> pytest --cov-append
======================================================================= test session starts ========================================================================
platform linux -- Python 3.12.5, pytest-8.3.2, pluggy-1.5.0 -- /tmp/sampleproject/.tox/py312/bin/python3
cachedir: .tox/py312/.pytest_cache
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: cov-5.0.0
collected 1 item

tests/test_simple.py::TestSimple::test_add_one PASSED                                                                                                        [100%]

---------- coverage: platform linux, python 3.12.5-final-0 -----------
Name                     Stmts   Miss  Cover
--------------------------------------------
src/sample/__init__.py       2      1    50%
src/sample/simple.py         2      0   100%
--------------------------------------------
TOTAL                        4      1    75%


======================================================================== 1 passed in 0.06s =========================================================================
  py311: OK (2.22=setup[1.85]+cmd[0.02,0.35] seconds)
  py312: OK (1.45=setup[1.05]+cmd[0.02,0.38] seconds)
  congratulations :) (3.74 seconds)
```
