# rye

パッケージ管理、依存関係管理、タスクランナ機能があるプロジェクト管理ツールである。

- ビルドシステム: `hatchling`
- パッケージマネージャ:  `uv`
- フォーマッタ・リンタ: `ruff`
- テスト: `pytest`

複数の仮想環境を操作する機能はない。

ツールを専用の仮想環境にインストールする機能がある。

## インストール

インストーラをタウンロードして対話形式でインストールする。

```sh
> curl -sSf https://rye.astral.sh/get | bash
This script will automatically download and install rye (latest) for you.
warning: this script is running as root.  This is dangerous and unnecessary!
######################################################################## 100.0%
Welcome to Rye!

This installer will install rye to /root/.rye
This path can be changed by exporting the RYE_HOME environment variable.

Details:
  Rye Version: 0.39.0
  Platform: linux (x86_64)

? Continue? ・ yes
? Select the preferred package installer ・ uv (fast, recommended)
? What should running `python` or `python3` do when you are not inside a Rye managed project? ・ Run a Python installed and managed by Rye
? Which version of Python should be used as default toolchain? ・ cpython@3.12
Installed binary to /root/.rye/shims/rye
Bootstrapping rye internals
Fetching requested internal toolchain 'cpython@3.12.5'
Downloading cpython@3.12.5
Checking checksum
Unpacking
Downloaded cpython@3.12.5
Updated self-python installation at /root/.rye/self

The rye directory /root/.rye/shims was not detected on PATH.
It is highly recommended that you add it.
? Should the installer add Rye to PATH via .profile? ・ yes
Added to PATH.
note: for this to take effect you will need to restart your shell or run this manually:

    source "$HOME/.rye/env"

For more information read https://rye.astral.sh/guide/installation/

All done!
```

## バージョン

```sh
> rye --version
rye 0.39.0
commit: 0.39.0 (bf3ccf818 2024-08-21)
platform: linux (x86_64)
self-python: cpython@3.12.5
symlink support: true
uv enabled: true
```

## プロジェクトの作成

プロジェクトを作成する。

```sh
> rye init
```

例: [sampleproject](https://github.com/pypa/sampleproject) を管理下にする。

`rye init` は実行できない。

```sh
> rey sync
Downloading cpython@3.8.19
Checking checksum
Unpacking
Downloaded cpython@3.8.19
Initializing new virtualenv in /tmp/sampleproject/.venv
Python version: cpython@3.8.19
Generating production lockfile: /tmp/sampleproject/requirements.lock
Generating dev lockfile: /tmp/sampleproject/requirements-dev.lock
Installing dependencies
Resolved 2 packages in 4ms
   Built sampleproject @ file:///tmp/sampleproject
Prepared 2 packages in 1.46s
Installed 2 packages in 4ms
 + peppercorn==0.6
 + sampleproject==3.0.0 (from file:///tmp/sampleproject)
Done!
```

`sampleproject` が editable モードでインストールされた仮想環境が作成される。

```sh
> rye list
peppercorn==0.6
-e file:///tmp/sampleproject
```

## 環境の作成

環境に入る。

```sh
> source .venv/bin/activate
(sampleproject) >
```

## 依存関係

任意のグループを指定して依存関係を追加することはできない。

`--dev` はツール独自の `tool.rye.dev-dependencies` に追加される。
`--optional <GROUP>` は `project.optional-dependecies.<GROUP>` に追加される。

`rye lock` のとき `project.optional-dependecies.<GROUP>` は `--feature <GROUP>` で指定できるが、
*requirements.lock* 内でグループの区別はないため、グループを選択的にインストールはできない。

### オプショナルなパッケージを追加

グループ dev にパッケージを追加する。

```sh
> rye add --dev ruff
Added ruff>=0.6.4 as dev dependency
Reusing already existing virtualenv
Generating production lockfile: /tmp/sampleproject/requirements.lock
Generating dev lockfile: /tmp/sampleproject/requirements-dev.lock
Installing dependencies
Resolved 3 packages in 8ms
   Built sampleproject @ file:///tmp/sampleproject
Prepared 1 package in 900ms
Uninstalled 1 package in 0.30ms
Installed 2 packages in 4ms
 + ruff==0.6.4
 ~ sampleproject==3.0.0 (from file:///tmp/sampleproject)
Done!
```

*pyproject.tml* に下記が追加される。

```toml
[tool.rye]
dev-dependencies = [
    "ruff>=0.6.4",
]
```

グループ testing にパッケージを追加する。

```sh
> rye add --optional testing pytest coverage
Added pytest>=8.3.2 as optional (testing) dependency
Added coverage>=7.6.1 as optional (testing) dependency
Reusing already existing virtualenv
Generating production lockfile: /tmp/sampleproject/requirements.lock
Generating dev lockfile: /tmp/sampleproject/requirements-dev.lock
Installing dependencies
Resolved 3 packages in 9ms
   Built sampleproject @ file:///tmp/sampleproject
Prepared 1 package in 954ms
Uninstalled 1 package in 0.45ms
Installed 1 package in 5ms
 ~ sampleproject==3.0.0 (from file:///tmp/sampleproject)
Done!
```

*pyproject.tml* に下記が追加される。

```toml
[project.optional-dependencies]
testing = [
    "pytest>=8.3.2",
    "coverage>=7.6.1",
]
```

*requirements.lock*, *requirements-dev.lock* が作成される。

## ビルド

パッケージを生成する。

```sh
> rye build
Python version: cpython@3.8.19
building sampleproject
* Creating isolated environment: venv+uv...
* Using external uv from /root/.rye/uv/0.3.0/uv
* Installing packages in isolated environment:
  - setuptools
* Getting build dependencies for sdist...
running egg_info
writing src/sampleproject.egg-info/PKG-INFO
writing dependency_links to src/sampleproject.egg-info/dependency_links.txt
writing entry points to src/sampleproject.egg-info/entry_points.txt
writing requirements to src/sampleproject.egg-info/requires.txt
writing top-level names to src/sampleproject.egg-info/top_level.txt
reading manifest file 'src/sampleproject.egg-info/SOURCES.txt'
adding license file 'LICENSE.txt'
writing manifest file 'src/sampleproject.egg-info/SOURCES.txt'
* Building sdist...
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
* Building wheel from sdist
* Creating isolated environment: venv+uv...
* Using external uv from /root/.rye/uv/0.3.0/uv
* Installing packages in isolated environment:
  - setuptools
* Getting build dependencies for wheel...
running egg_info
writing src/sampleproject.egg-info/PKG-INFO
writing dependency_links to src/sampleproject.egg-info/dependency_links.txt
writing entry points to src/sampleproject.egg-info/entry_points.txt
writing requirements to src/sampleproject.egg-info/requires.txt
writing top-level names to src/sampleproject.egg-info/top_level.txt
reading manifest file 'src/sampleproject.egg-info/SOURCES.txt'
adding license file 'LICENSE.txt'
writing manifest file 'src/sampleproject.egg-info/SOURCES.txt'
* Building wheel...
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
Copying src/sampleproject.egg-info to build/bdist.linux-x86_64/wheel/./sampleproject-3.0.0-py3.8.egg-info
running install_scripts
creating build/bdist.linux-x86_64/wheel/sampleproject-3.0.0.dist-info/WHEEL
creating '/tmp/sampleproject/dist/.tmp-3orw_bdh/sampleproject-3.0.0-py3-none-any.whl' and adding 'build/bdist.linux-x86_64/wheel' to it
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
Successfully built sampleproject-3.0.0.tar.gz and sampleproject-3.0.0-py3-none-any.whl
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
      111  09-08-2024 04:58   sample/__init__.py
        9  09-08-2024 04:58   sample/package_data.dat
       43  09-08-2024 04:58   sample/simple.py
     1081  09-08-2024 05:11   sampleproject-3.0.0.dist-info/LICENSE.txt
     4393  09-08-2024 05:11   sampleproject-3.0.0.dist-info/METADATA
       91  09-08-2024 05:11   sampleproject-3.0.0.dist-info/WHEEL
       39  09-08-2024 05:11   sampleproject-3.0.0.dist-info/entry_points.txt
        7  09-08-2024 05:11   sampleproject-3.0.0.dist-info/top_level.txt
      742  09-08-2024 05:11   sampleproject-3.0.0.dist-info/RECORD
---------                     -------
     6516                     9 files
```

## フォーマット

ソースコードをフォーマットする。

```sh
> rye fmt
2 files reformatted, 3 files left unchanged
```

## リント

ソースコードを静的解析する。

```sh
> rye lint --fix
All checks passed!
```

## テスト

`pytest` をプロジェクトに追加する。

```sh
> rye add --dev pytest pytest-cov
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
> rye test
Running tests for sampleproject (/tmp/sampleproject)
========================================================================= test session starts ==========================================================================
platform linux -- Python 3.8.19, pytest-8.3.2, pluggy-1.5.0 -- /tmp/sampleproject/.venv/bin/python3
cachedir: .pytest_cache
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: cov-5.0.0
collected 1 item

tests/test_simple.py::TestSimple::test_add_one PASSED                                                                                                            [100%]

---------- coverage: platform linux, python 3.8.19-final-0 -----------
Name                     Stmts   Miss  Cover
--------------------------------------------
src/sample/__init__.py       2      1    50%
src/sample/simple.py         2      0   100%
--------------------------------------------
TOTAL                        4      1    75%


========================================================================== 1 passed in 0.09s ===========================================================================
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
commands =
    pip install -r requirements-dev.lock
    pytest --cov-append
"""
EOF
```

テストを実行する。

```sh
> tox
.pkg: install_requires> python -I -m pip install setuptools
.pkg: _optional_hooks> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: get_requires_for_build_sdist> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: build_sdist> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True setuptools.build_meta
py311: install_package_deps> python -I -m pip install peppercorn
py311: install_package> python -I -m pip install --force-reinstall --no-deps /tmp/sampleproject/.tox/.tmp/package/1/sampleproject-3.0.0.tar.gz
py311: commands[0]> pip install -r requirements-dev.lock
Obtaining file:///. (from -r requirements-dev.lock (line 12))
  Installing build dependencies ... done
  Checking if build backend supports build_editable ... done
  Getting requirements to build editable ... done
  Preparing editable metadata (pyproject.toml) ... done
Collecting coverage==7.6.1 (from -r requirements-dev.lock (line 13))
  Downloading coverage-7.6.1-cp311-cp311-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (8.3 kB)
Collecting exceptiongroup==1.2.2 (from -r requirements-dev.lock (line 15))
  Downloading exceptiongroup-1.2.2-py3-none-any.whl.metadata (6.6 kB)
Collecting iniconfig==2.0.0 (from -r requirements-dev.lock (line 17))
  Downloading iniconfig-2.0.0-py3-none-any.whl.metadata (2.6 kB)
Collecting packaging==24.1 (from -r requirements-dev.lock (line 19))
  Downloading packaging-24.1-py3-none-any.whl.metadata (3.2 kB)
Requirement already satisfied: peppercorn==0.6 in ./.tox/py311/lib/python3.11/site-packages (from -r requirements-dev.lock (line 21)) (0.6)
Collecting pluggy==1.5.0 (from -r requirements-dev.lock (line 23))
  Downloading pluggy-1.5.0-py3-none-any.whl.metadata (4.8 kB)
Collecting pytest==8.3.2 (from -r requirements-dev.lock (line 25))
  Downloading pytest-8.3.2-py3-none-any.whl.metadata (7.5 kB)
Collecting pytest-cov==5.0.0 (from -r requirements-dev.lock (line 27))
  Downloading pytest_cov-5.0.0-py3-none-any.whl.metadata (27 kB)
Collecting tomli==2.0.1 (from -r requirements-dev.lock (line 28))
  Downloading tomli-2.0.1-py3-none-any.whl.metadata (8.9 kB)
Downloading coverage-7.6.1-cp311-cp311-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_17_x86_64.manylinux2014_x86_64.whl (238 kB)
Downloading exceptiongroup-1.2.2-py3-none-any.whl (16 kB)
Downloading iniconfig-2.0.0-py3-none-any.whl (5.9 kB)
Downloading packaging-24.1-py3-none-any.whl (53 kB)
Downloading pluggy-1.5.0-py3-none-any.whl (20 kB)
Downloading pytest-8.3.2-py3-none-any.whl (341 kB)
Downloading pytest_cov-5.0.0-py3-none-any.whl (21 kB)
Downloading tomli-2.0.1-py3-none-any.whl (12 kB)
Building wheels for collected packages: sampleproject
  Building editable for sampleproject (pyproject.toml) ... done
  Created wheel for sampleproject: filename=sampleproject-3.0.0-0.editable-py3-none-any.whl size=4264 sha256=6b43da4b617f03978535fecc663c8b09d9151a604ea4c8b1691f84eda56bea84
  Stored in directory: /tmp/pip-ephem-wheel-cache-mrc_xktb/wheels/97/54/f5/d849319cdfa096e074df352654ee2e7c919da8951f090690c6
Successfully built sampleproject
Installing collected packages: tomli, sampleproject, pluggy, packaging, iniconfig, exceptiongroup, coverage, pytest, pytest-cov
  Attempting uninstall: sampleproject
    Found existing installation: sampleproject 3.0.0
    Uninstalling sampleproject-3.0.0:
      Successfully uninstalled sampleproject-3.0.0
Successfully installed coverage-7.6.1 exceptiongroup-1.2.2 iniconfig-2.0.0 packaging-24.1 pluggy-1.5.0 pytest-8.3.2 pytest-cov-5.0.0 sampleproject-3.0.0 tomli-2.0.1
py311: commands[1]> pytest --cov-append
======================================================================= test session starts ========================================================================
platform linux -- Python 3.11.7, pytest-8.3.2, pluggy-1.5.0 -- /tmp/sampleproject/.tox/py311/bin/python
cachedir: .tox/py311/.pytest_cache
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: cov-5.0.0
collected 1 item

tests/test_simple.py::TestSimple::test_add_one PASSED                                                                                                        [100%]

---------- coverage: platform linux, python 3.11.7-final-0 -----------
Name                     Stmts   Miss  Cover
--------------------------------------------
src/sample/__init__.py       2      1    50%
src/sample/simple.py         2      0   100%
--------------------------------------------
TOTAL                        4      1    75%


======================================================================== 1 passed in 0.05s =========================================================================
py311: OK ? in 13.81 seconds
py312: install_package_deps> python -I -m pip install peppercorn
py312: install_package> python -I -m pip install --force-reinstall --no-deps /tmp/sampleproject/.tox/.tmp/package/2/sampleproject-3.0.0.tar.gz
py312: commands[0]> pip install -r requirements-dev.lock
Obtaining file:///. (from -r requirements-dev.lock (line 12))
  Installing build dependencies ... done
  Checking if build backend supports build_editable ... done
  Getting requirements to build editable ... done
  Preparing editable metadata (pyproject.toml) ... done
Collecting coverage==7.6.1 (from -r requirements-dev.lock (line 13))
  Downloading coverage-7.6.1-cp312-cp312-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (8.3 kB)
Collecting exceptiongroup==1.2.2 (from -r requirements-dev.lock (line 15))
  Using cached exceptiongroup-1.2.2-py3-none-any.whl.metadata (6.6 kB)
Collecting iniconfig==2.0.0 (from -r requirements-dev.lock (line 17))
  Using cached iniconfig-2.0.0-py3-none-any.whl.metadata (2.6 kB)
Collecting packaging==24.1 (from -r requirements-dev.lock (line 19))
  Using cached packaging-24.1-py3-none-any.whl.metadata (3.2 kB)
Requirement already satisfied: peppercorn==0.6 in ./.tox/py312/lib/python3.12/site-packages (from -r requirements-dev.lock (line 21)) (0.6)
Collecting pluggy==1.5.0 (from -r requirements-dev.lock (line 23))
  Using cached pluggy-1.5.0-py3-none-any.whl.metadata (4.8 kB)
Collecting pytest==8.3.2 (from -r requirements-dev.lock (line 25))
  Using cached pytest-8.3.2-py3-none-any.whl.metadata (7.5 kB)
Collecting pytest-cov==5.0.0 (from -r requirements-dev.lock (line 27))
  Using cached pytest_cov-5.0.0-py3-none-any.whl.metadata (27 kB)
Collecting tomli==2.0.1 (from -r requirements-dev.lock (line 28))
  Using cached tomli-2.0.1-py3-none-any.whl.metadata (8.9 kB)
Downloading coverage-7.6.1-cp312-cp312-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_17_x86_64.manylinux2014_x86_64.whl (239 kB)
Using cached exceptiongroup-1.2.2-py3-none-any.whl (16 kB)
Using cached iniconfig-2.0.0-py3-none-any.whl (5.9 kB)
Using cached packaging-24.1-py3-none-any.whl (53 kB)
Using cached pluggy-1.5.0-py3-none-any.whl (20 kB)
Using cached pytest-8.3.2-py3-none-any.whl (341 kB)
Using cached pytest_cov-5.0.0-py3-none-any.whl (21 kB)
Using cached tomli-2.0.1-py3-none-any.whl (12 kB)
Building wheels for collected packages: sampleproject
  Building editable for sampleproject (pyproject.toml) ... done
  Created wheel for sampleproject: filename=sampleproject-3.0.0-0.editable-py3-none-any.whl size=4264 sha256=6607c59d78650f7a8b0534e5bd21301575c1eff5bfca7890b6e8f438bdd7761f
  Stored in directory: /tmp/pip-ephem-wheel-cache-gu1c4p4k/wheels/8b/19/c8/73a63a20645e0f1ed9aae9dd5d459f0f7ad2332bb27cba6c0f
Successfully built sampleproject
Installing collected packages: tomli, sampleproject, pluggy, packaging, iniconfig, exceptiongroup, coverage, pytest, pytest-cov
  Attempting uninstall: sampleproject
    Found existing installation: sampleproject 3.0.0
    Uninstalling sampleproject-3.0.0:
      Successfully uninstalled sampleproject-3.0.0
Successfully installed coverage-7.6.1 exceptiongroup-1.2.2 iniconfig-2.0.0 packaging-24.1 pluggy-1.5.0 pytest-8.3.2 pytest-cov-5.0.0 sampleproject-3.0.0 tomli-2.0.1
py312: commands[1]> pytest --cov-append
======================================================================= test session starts ========================================================================
platform linux -- Python 3.12.1, pytest-8.3.2, pluggy-1.5.0 -- /tmp/sampleproject/.tox/py312/bin/python
cachedir: .tox/py312/.pytest_cache
rootdir: /tmp/sampleproject
configfile: pyproject.toml
plugins: cov-5.0.0
collected 1 item

tests/test_simple.py::TestSimple::test_add_one PASSED                                                                                                        [100%]

---------- coverage: platform linux, python 3.12.1-final-0 -----------
Name                     Stmts   Miss  Cover
--------------------------------------------
src/sample/__init__.py       2      1    50%
src/sample/simple.py         2      0   100%
--------------------------------------------
TOTAL                        4      1    75%


======================================================================== 1 passed in 0.06s =========================================================================
  py311: OK (13.81=setup[8.25]+cmd[5.20,0.35] seconds)
  py312: OK (10.07=setup[4.65]+cmd[5.02,0.40] seconds)
  congratulations :) (23.96 seconds)
```

rye が管理するインタプリタを使用する。システムに virtualenv-rye-discovery をインストールする。

```sh
> python3.9 -m pip install git+https://github.com/bluss/virtualenv-rye-discovery.git@0.3.0
```

環境変数 `VIRTUALENV_DISCOVERY` を追加する。

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
    VIRTUALENV_DISCOVERY=rye
commands =
    pip install -r requirements-dev.lock
    pytest --cov-append
"""
EOF
```

システムの tox を実行する。

```sh
> python3.9 -m tox
.pkg: _optional_hooks> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: get_requires_for_build_sdist> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True setuptools.build_meta
.pkg: build_sdist> python /usr/local/lib/python3.9/site-packages/pyproject_api/_backend.py True setuptools.build_meta
py311: install_package> python -I -m pip install --force-reinstall --no-deps /tmp/sampleproject/.tox/.tmp/package/8/sampleproject-3.0.0.tar.gz
py311: commands[0]> pip install -r requirements-dev.lock
Obtaining file:///. (from -r requirements-dev.lock (line 12))
  Installing build dependencies ... done
  Checking if build backend supports build_editable ... done
  Getting requirements to build editable ... done
  Preparing editable metadata (pyproject.toml) ... done
Requirement already satisfied: coverage==7.6.1 in ./.tox/py311/lib/python3.11/site-packages (from -r requirements-dev.lock (line 13)) (7.6.1)
Requirement already satisfied: exceptiongroup==1.2.2 in ./.tox/py311/lib/python3.11/site-packages (from -r requirements-dev.lock (line 15)) (1.2.2)
Requirement already satisfied: iniconfig==2.0.0 in ./.tox/py311/lib/python3.11/site-packages (from -r requirements-dev.lock (line 17)) (2.0.0)
Requirement already satisfied: packaging==24.1 in ./.tox/py311/lib/python3.11/site-packages (from -r requirements-dev.lock (line 19)) (24.1)
Requirement already satisfied: peppercorn==0.6 in ./.tox/py311/lib/python3.11/site-packages (from -r requirements-dev.lock (line 21)) (0.6)
Requirement already satisfied: pluggy==1.5.0 in ./.tox/py311/lib/python3.11/site-packages (from -r requirements-dev.lock (line 23)) (1.5.0)
Requirement already satisfied: pytest==8.3.2 in ./.tox/py311/lib/python3.11/site-packages (from -r requirements-dev.lock (line 25)) (8.3.2)
Requirement already satisfied: pytest-cov==5.0.0 in ./.tox/py311/lib/python3.11/site-packages (from -r requirements-dev.lock (line 27)) (5.0.0)
Requirement already satisfied: tomli==2.0.1 in ./.tox/py311/lib/python3.11/site-packages (from -r requirements-dev.lock (line 28)) (2.0.1)
Building wheels for collected packages: sampleproject
  Building editable for sampleproject (pyproject.toml) ... done
  Created wheel for sampleproject: filename=sampleproject-3.0.0-0.editable-py3-none-any.whl size=4264 sha256=fcc477ad070516185234cffc00ff71274e3ea0f588328c33b1cac5de84719302
  Stored in directory: /tmp/pip-ephem-wheel-cache-guqgod07/wheels/97/54/f5/d849319cdfa096e074df352654ee2e7c919da8951f090690c6
Successfully built sampleproject
Installing collected packages: sampleproject
  Attempting uninstall: sampleproject
    Found existing installation: sampleproject 3.0.0
    Uninstalling sampleproject-3.0.0:
      Successfully uninstalled sampleproject-3.0.0
Successfully installed sampleproject-3.0.0
py311: commands[1]> pytest --cov-append
======================================================================= test session starts ========================================================================
platform linux -- Python 3.11.9, pytest-8.3.2, pluggy-1.5.0 -- /tmp/sampleproject/.tox/py311/bin/python
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
py311: OK ? in 7.09 seconds
py312: install_package> python -I -m pip install --force-reinstall --no-deps /tmp/sampleproject/.tox/.tmp/package/9/sampleproject-3.0.0.tar.gz
py312: commands[0]> pip install -r requirements-dev.lock
Obtaining file:///. (from -r requirements-dev.lock (line 12))
  Installing build dependencies ... done
  Checking if build backend supports build_editable ... done
  Getting requirements to build editable ... done
  Preparing editable metadata (pyproject.toml) ... done
Requirement already satisfied: coverage==7.6.1 in ./.tox/py312/lib/python3.12/site-packages (from -r requirements-dev.lock (line 13)) (7.6.1)
Requirement already satisfied: exceptiongroup==1.2.2 in ./.tox/py312/lib/python3.12/site-packages (from -r requirements-dev.lock (line 15)) (1.2.2)
Requirement already satisfied: iniconfig==2.0.0 in ./.tox/py312/lib/python3.12/site-packages (from -r requirements-dev.lock (line 17)) (2.0.0)
Requirement already satisfied: packaging==24.1 in ./.tox/py312/lib/python3.12/site-packages (from -r requirements-dev.lock (line 19)) (24.1)
Requirement already satisfied: peppercorn==0.6 in ./.tox/py312/lib/python3.12/site-packages (from -r requirements-dev.lock (line 21)) (0.6)
Requirement already satisfied: pluggy==1.5.0 in ./.tox/py312/lib/python3.12/site-packages (from -r requirements-dev.lock (line 23)) (1.5.0)
Requirement already satisfied: pytest==8.3.2 in ./.tox/py312/lib/python3.12/site-packages (from -r requirements-dev.lock (line 25)) (8.3.2)
Requirement already satisfied: pytest-cov==5.0.0 in ./.tox/py312/lib/python3.12/site-packages (from -r requirements-dev.lock (line 27)) (5.0.0)
Requirement already satisfied: tomli==2.0.1 in ./.tox/py312/lib/python3.12/site-packages (from -r requirements-dev.lock (line 28)) (2.0.1)
Building wheels for collected packages: sampleproject
  Building editable for sampleproject (pyproject.toml) ... done
  Created wheel for sampleproject: filename=sampleproject-3.0.0-0.editable-py3-none-any.whl size=4264 sha256=ba2945487a64c550b30788d22a0f19e18b4250c60ff885c3a0d80d429d113334
  Stored in directory: /tmp/pip-ephem-wheel-cache-rc2esazy/wheels/8b/19/c8/73a63a20645e0f1ed9aae9dd5d459f0f7ad2332bb27cba6c0f
Successfully built sampleproject
Installing collected packages: sampleproject
  Attempting uninstall: sampleproject
    Found existing installation: sampleproject 3.0.0
    Uninstalling sampleproject-3.0.0:
      Successfully uninstalled sampleproject-3.0.0
Successfully installed sampleproject-3.0.0
py312: commands[1]> pytest --cov-append
======================================================================= test session starts ========================================================================
platform linux -- Python 3.12.5, pytest-8.3.2, pluggy-1.5.0 -- /tmp/sampleproject/.tox/py312/bin/python
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
  py311: OK (7.09=setup[3.75]+cmd[3.02,0.33] seconds)
  py312: OK (6.72=setup[3.07]+cmd[3.28,0.36] seconds)
  congratulations :) (13.88 seconds)
```
