# pip

## インストール

python に含まれている。

## バージョン

```sh
> pip --version
pip 24.2 from /tmp/.venv/lib64/python3.9/site-packages/pip (python 3.9)
```

## install

パッケージのインストールやアップデートを行う。

インストール元は PyPI, バージョン管理リポジトリ、ローカルファイルが指定できる。
パッケージは *requrements file* も指定できる。

```sh
> pip install <package>
> pip install -r <requrements file>
> pip install git+https://github.com/<owner>/<repository>
> pip install <local file path>
```

editable mode は *setup.py* が必要になる。

```
ERROR: File "setup.py" or "setup.cfg" not found. Directory cannot be installed in editable mode: /tmp/sampleproject
(A "pyproject.toml" file was found, but editable mode currently requires a setuptools-based build.)
```

例: `build` を PyPI からインストールする。

```sh
> pip install build
Collecting build
  Downloading build-1.2.2-py3-none-any.whl.metadata (6.2 kB)
Collecting packaging>=19.1 (from build)
  Using cached packaging-24.1-py3-none-any.whl.metadata (3.2 kB)
Collecting pyproject_hooks (from build)
  Downloading pyproject_hooks-1.1.0-py3-none-any.whl.metadata (1.3 kB)
Collecting importlib-metadata>=4.6 (from build)
  Downloading importlib_metadata-8.4.0-py3-none-any.whl.metadata (4.7 kB)
Collecting tomli>=1.1.0 (from build)
  Using cached tomli-2.0.1-py3-none-any.whl.metadata (8.9 kB)
Collecting zipp>=0.5 (from importlib-metadata>=4.6->build)
  Downloading zipp-3.20.1-py3-none-any.whl.metadata (3.7 kB)
Downloading build-1.2.2-py3-none-any.whl (22 kB)
Downloading importlib_metadata-8.4.0-py3-none-any.whl (26 kB)
Using cached packaging-24.1-py3-none-any.whl (53 kB)
Using cached tomli-2.0.1-py3-none-any.whl (12 kB)
Downloading pyproject_hooks-1.1.0-py3-none-any.whl (9.2 kB)
Downloading zipp-3.20.1-py3-none-any.whl (9.0 kB)
Installing collected packages: zipp, tomli, pyproject_hooks, packaging, importlib-metadata, build
Successfully installed build-1.2.2 importlib-metadata-8.4.0 packaging-24.1 pyproject_hooks-1.1.0 tomli-2.0.1 zipp-3.20.1
```

例: `build` をバージョン管理リポジトリからタグを指定してインストールする。

```sh
> pip install git+https://github.com/pypa/build.git@1.2.1
Collecting git+https://github.com/pypa/build.git@1.2.1
  Cloning https://github.com/pypa/build.git (to revision 1.2.1) to /tmp/pip-req-build-_l9nmc1t
  Running command git clone -q https://github.com/pypa/build.git /tmp/pip-req-build-_l9nmc1t
  Running command git checkout -q 92a468982852027cd2342a6d79959bfa6e37de4b
  Resolved https://github.com/pypa/build.git to commit 92a468982852027cd2342a6d79959bfa6e37de4b
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
    Preparing wheel metadata ... done
Collecting importlib-metadata>=4.6
  Downloading importlib_metadata-8.4.0-py3-none-any.whl (26 kB)
Collecting pyproject_hooks
  Downloading pyproject_hooks-1.1.0-py3-none-any.whl (9.2 kB)
Collecting packaging>=19.1
  Downloading packaging-24.1-py3-none-any.whl (53 kB)
Collecting tomli>=1.1.0
  Downloading tomli-2.0.1-py3-none-any.whl (12 kB)
Collecting zipp>=0.5
  Downloading zipp-3.20.1-py3-none-any.whl (9.0 kB)
Building wheels for collected packages: build
  Building wheel for build (PEP 517) ... done
  Created wheel for build: filename=build-1.2.1-py3-none-any.whl size=21911 sha256=02990ff361903bb2f7f4dbabf194d7bae43b1b4973d6d106046fe7a489d37845
  Stored in directory: /tmp/pip-ephem-wheel-cache-vjgilb2l/wheels/e9/18/3b/051e320a9e1ffcb892d16cf18bd3cd15711b12bfa619ad5330
Successfully built build
Installing collected packages: zipp, tomli, pyproject-hooks, packaging, importlib-metadata, build
Successfully installed build-1.2.1 importlib-metadata-8.4.0 packaging-24.1 pyproject-hooks-1.1.0 tomli-2.0.1 zipp-3.20.1```
```

例: `build` をアップデートする。

```sh
> pip install --upgrade build
Requirement already satisfied: build in ./.venv/lib/python3.9/site-packages (1.2.1)
Collecting build
  Using cached build-1.2.2-py3-none-any.whl.metadata (6.2 kB)
Requirement already satisfied: packaging>=19.1 in ./.venv/lib/python3.9/site-packages (from build) (24.1)
Requirement already satisfied: pyproject_hooks in ./.venv/lib/python3.9/site-packages (from build) (1.1.0)
Requirement already satisfied: importlib-metadata>=4.6 in ./.venv/lib/python3.9/site-packages (from build) (8.4.0)
Requirement already satisfied: tomli>=1.1.0 in ./.venv/lib/python3.9/site-packages (from build) (2.0.1)
Requirement already satisfied: zipp>=0.5 in ./.venv/lib/python3.9/site-packages (from importlib-metadata>=4.6->build) (3.20.1)
Using cached build-1.2.2-py3-none-any.whl (22 kB)
Installing collected packages: build
  Attempting uninstall: build
    Found existing installation: build 1.2.1
    Uninstalling build-1.2.1:
      Successfully uninstalled build-1.2.1
Successfully installed build-1.2.2
```

## download

パッケージのダウンロードを行う。

ダウンロード元は PyPI, バージョン管理リポジトリ、ローカルファイルが指定できる。
パッケージは *requrements file* も指定できる。

```sh
> pip download <package>
> pip download -r <requrements file>
> pip download git+https://github.com/<owner>/<repository>
> pip download <local file path>
```


例: `build` を PyPI からダウンロードする。

```sh
> pip download build
Collecting build
  Using cached build-1.2.2-py3-none-any.whl.metadata (6.2 kB)
Collecting packaging>=19.1 (from build)
  Using cached packaging-24.1-py3-none-any.whl.metadata (3.2 kB)
Collecting pyproject_hooks (from build)
  Using cached pyproject_hooks-1.1.0-py3-none-any.whl.metadata (1.3 kB)
Collecting importlib-metadata>=4.6 (from build)
  Using cached importlib_metadata-8.4.0-py3-none-any.whl.metadata (4.7 kB)
Collecting tomli>=1.1.0 (from build)
  Using cached tomli-2.0.1-py3-none-any.whl.metadata (8.9 kB)
Collecting zipp>=0.5 (from importlib-metadata>=4.6->build)
  Using cached zipp-3.20.1-py3-none-any.whl.metadata (3.7 kB)
Using cached build-1.2.2-py3-none-any.whl (22 kB)
Using cached importlib_metadata-8.4.0-py3-none-any.whl (26 kB)
Using cached packaging-24.1-py3-none-any.whl (53 kB)
Using cached tomli-2.0.1-py3-none-any.whl (12 kB)
Using cached pyproject_hooks-1.1.0-py3-none-any.whl (9.2 kB)
Using cached zipp-3.20.1-py3-none-any.whl (9.0 kB)
Saved ./build-1.2.2-py3-none-any.whl
Saved ./importlib_metadata-8.4.0-py3-none-any.whl
Saved ./packaging-24.1-py3-none-any.whl
Saved ./tomli-2.0.1-py3-none-any.whl
Saved ./pyproject_hooks-1.1.0-py3-none-any.whl
Saved ./zipp-3.20.1-py3-none-any.whl
Successfully downloaded build importlib-metadata packaging tomli pyproject_hooks zipp
```

例: `build` をバージョン管理リポジトリからタグを指定してダウンロードする。

```sh
> pip download git+https://github.com/pypa/build.git@1.2.1
Collecting git+https://github.com/pypa/build.git@1.2.1
  Cloning https://github.com/pypa/build.git (to revision 1.2.1) to /tmp/pip-req-build-6z0zwfet
  Running command git clone --filter=blob:none --quiet https://github.com/pypa/build.git /tmp/pip-req-build-6z0zwfet
  Running command git checkout -q 92a468982852027cd2342a6d79959bfa6e37de4b
  Resolved https://github.com/pypa/build.git to commit 92a468982852027cd2342a6d79959bfa6e37de4b
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
  Preparing metadata (pyproject.toml) ... done
Collecting packaging>=19.1 (from build==1.2.1)
  Using cached packaging-24.1-py3-none-any.whl.metadata (3.2 kB)
Collecting pyproject_hooks (from build==1.2.1)
  Using cached pyproject_hooks-1.1.0-py3-none-any.whl.metadata (1.3 kB)
Collecting importlib-metadata>=4.6 (from build==1.2.1)
  Using cached importlib_metadata-8.4.0-py3-none-any.whl.metadata (4.7 kB)
Collecting tomli>=1.1.0 (from build==1.2.1)
  Using cached tomli-2.0.1-py3-none-any.whl.metadata (8.9 kB)
Collecting zipp>=0.5 (from importlib-metadata>=4.6->build==1.2.1)
  Using cached zipp-3.20.1-py3-none-any.whl.metadata (3.7 kB)
Using cached importlib_metadata-8.4.0-py3-none-any.whl (26 kB)
Using cached packaging-24.1-py3-none-any.whl (53 kB)
Using cached tomli-2.0.1-py3-none-any.whl (12 kB)
Using cached pyproject_hooks-1.1.0-py3-none-any.whl (9.2 kB)
Using cached zipp-3.20.1-py3-none-any.whl (9.0 kB)
Saved ./build-1.2.1.zip
Saved ./importlib_metadata-8.4.0-py3-none-any.whl
Saved ./packaging-24.1-py3-none-any.whl
Saved ./tomli-2.0.1-py3-none-any.whl
Saved ./pyproject_hooks-1.1.0-py3-none-any.whl
Saved ./zipp-3.20.1-py3-none-any.whl
Successfully downloaded build importlib-metadata packaging tomli pyproject_hooks zipp
```

## uninstall

パッケージをアンインストールする。
パッケージは *requrements file* も指定できる。

```sh
> pip uninstall <package>
> pip uninstall -r <requrements file>
```

依存関係にあるパッケージには影響しない。

例: `build` をアンインストールする。

```sh
> pip uninstall build
Found existing installation: build 1.2.2
Uninstalling build-1.2.2:
  Would remove:
    /tmp/sampleproject/.venv/bin/pyproject-build
    /tmp/sampleproject/.venv/lib/python3.9/site-packages/build-1.2.2.dist-info/*
    /tmp/sampleproject/.venv/lib/python3.9/site-packages/build/*
Proceed (Y/n)?
  Successfully uninstalled build-1.2.2
```

## freeze

インストールされたパッケージから *requrements file* を生成する。

```sh
> pip freeze
```

例: `build` をインストールした状態で *requrements file* を生成する。

```sh
> pip freeze
build==1.2.2
importlib_metadata==8.4.0
packaging==24.1
pyproject_hooks==1.1.0
tomli==2.0.1
zipp==3.20.1
```

## list

インストールされたパッケージを表示する。

```sh
> pip list
```

例: `build` をインストールした状態で json 形式で表示する。

```sh
> pip list --format=json | jq
[
  {
    "name": "build",
    "version": "1.2.2"
  },
  {
    "name": "importlib_metadata",
    "version": "8.4.0"
  },
  {
    "name": "packaging",
    "version": "24.1"
  },
  {
    "name": "pip",
    "version": "24.2"
  },
  {
    "name": "pyproject_hooks",
    "version": "1.1.0"
  },
  {
    "name": "setuptools",
    "version": "53.0.0"
  },
  {
    "name": "tomli",
    "version": "2.0.1"
  },
  {
    "name": "zipp",
    "version": "3.20.1"
  }
]
```

## show

インストールされたパッケージの情報を表示する。

```sh
> pip show <package>
```

例: `build` の情報を表示する。

```sh
> pip show build
Name: build
Version: 1.2.2
Summary: A simple, correct Python build frontend
Home-page: https://build.pypa.io
Author:
Author-email:
License:
Location: /tmp/sampleproject/.venv/lib/python3.9/site-packages
Requires: importlib-metadata, packaging, pyproject_hooks, tomli
Required-by:
```

## search

使用できない。

```
ERROR: XMLRPC request failed [code: -32500]
RuntimeError: PyPI no longer supports 'pip search' (or XML-RPC search).
Please use https://pypi.org/search (via a browser) instead.
See https://warehouse.pypa.io/api-reference/xml-rpc.html#deprecated-methods for more information.
```

## cache

`pip` コマンドが使用するキャッシュを管理する。

## index

パッケージの利用可能なバージョンを PyPI から取得する。

```sh
> pip index versions <package>
```

例: `build` の利用可能なバージョンを取得する。

```sh
> pip index versions build
build (1.2.2)
Available versions: 1.2.2, 1.2.1, 1.1.1, 1.0.3, 1.0.0, 0.10.0, 0.9.0, 0.8.0, 0.7.0, 0.6.0.post1, 0.5.1, 0.5.0, 0.4.0, 0.3.1.post1, 0.3.1, 0.3.0, 0.2.1, 0.2.0, 0.1.0, 0.0.4, 0.0.3.1, 0.0.2, 0.0.1
  INSTALLED: 1.2.2
  LATEST:    1.2.2
```

## wheel

wheel 形式のパッケージを作成する。

パッケージは PyPI, バージョン管理リポジトリ、ローカルファイルが指定できる。
パッケージは *requrements file* も指定できる。

```sh
> pip wheel <package>
> pip wheel -r <requrements file>
> pip wheel git+https://github.com/<owner>/<repository>
> pip wheel <local file path>
```

例: `build` の wheel パッケージを作成する。

```sh
> pip wheel build
Collecting build
  Using cached build-1.2.2-py3-none-any.whl.metadata (6.2 kB)
Collecting packaging>=19.1 (from build)
  Using cached packaging-24.1-py3-none-any.whl.metadata (3.2 kB)
Collecting pyproject_hooks (from build)
  Using cached pyproject_hooks-1.1.0-py3-none-any.whl.metadata (1.3 kB)
Collecting importlib-metadata>=4.6 (from build)
  Using cached importlib_metadata-8.4.0-py3-none-any.whl.metadata (4.7 kB)
Collecting tomli>=1.1.0 (from build)
  Using cached tomli-2.0.1-py3-none-any.whl.metadata (8.9 kB)
Collecting zipp>=0.5 (from importlib-metadata>=4.6->build)
  Using cached zipp-3.20.1-py3-none-any.whl.metadata (3.7 kB)
Using cached build-1.2.2-py3-none-any.whl (22 kB)
Using cached importlib_metadata-8.4.0-py3-none-any.whl (26 kB)
Using cached packaging-24.1-py3-none-any.whl (53 kB)
Using cached tomli-2.0.1-py3-none-any.whl (12 kB)
Using cached pyproject_hooks-1.1.0-py3-none-any.whl (9.2 kB)
Using cached zipp-3.20.1-py3-none-any.whl (9.0 kB)
Saved ./build-1.2.2-py3-none-any.whl
Saved ./importlib_metadata-8.4.0-py3-none-any.whl
Saved ./packaging-24.1-py3-none-any.whl
Saved ./tomli-2.0.1-py3-none-any.whl
Saved ./pyproject_hooks-1.1.0-py3-none-any.whl
Saved ./zipp-3.20.1-py3-none-any.whl
```

## hash

ファイルのハッシュ値を計算する。ファイルはパッケージ以外でも計算できる。

## completion

`pip` コマンドのコマンド補完するための処理を生成する。

```sh
> pip completion
```

例: bash のコマンド補完処理を生成する。

```sh
> pip completion --bash

# pip bash completion start
_pip_completion()
{
    COMPREPLY=( $( COMP_WORDS="${COMP_WORDS[*]}" \
                   COMP_CWORD=$COMP_CWORD \
                   PIP_AUTO_COMPLETE=1 $1 2>/dev/null ) )
}
complete -o default -F _pip_completion pip
# pip bash completion end
```

## debug

デバック情報を表示する。
