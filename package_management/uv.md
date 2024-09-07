# uv

## インストール

```sh
> curl -LsSf https://astral.sh/uv/install.sh | sh
downloading uv 0.4.7 x86_64-unknown-linux-gnu
installing to /root/.cargo/bin
  uv
  uvx
everything's installed!

To add $HOME/.cargo/bin to your PATH, either restart your shell or run:

    source $HOME/.cargo/env (sh, bash, zsh)
    source $HOME/.cargo/env.fish (fish)
```

## バージョン

```sh
> uv --version
uv 0.4.7
```

## compile

*requirements.in* から *requirements.txt* を生成する。

## sync

*requirements.txt* と環境を同期する。

## install

パッケージのインストールやアップデートを行う。

インストール元は PyPI, バージョン管理リポジトリ、ローカルファイルが指定できる。
パッケージは *requrements file* も指定できる。

```sh
> uv pip install <package>
> uv pip install -r <requrements file>
> uv pip install git+https://github.com/<owner>/<repository>
> uv pip install <local file path>
```

例: `build` を PyPI からインストールする。

```sh
> uv pip install build
Resolved 6 packages in 309ms
Prepared 6 packages in 86ms
Installed 6 packages in 42ms
 + build==1.2.2
 + importlib-metadata==8.4.0
 + packaging==24.1
 + pyproject-hooks==1.1.0
 + tomli==2.0.1
 + zipp==3.20.1
```

例: `build` をバージョン管理リポジトリからタグを指定してインストールする。

```sh
> uv pip install git+https://github.com/pypa/build.git@1.2.1
 Updated https://github.com/pypa/build.git (1e67c06)
Resolved 6 packages in 3.00s
   Built build @ git+https://github.com/pypa/build.git@1e67c062e9d1b1a6d5ffed621f4b29902bb764e5
Prepared 1 package in 291ms
Installed 6 packages in 15ms
 + build==1.2.1 (from git+https://github.com/pypa/build.git@1e67c062e9d1b1a6d5ffed621f4b29902bb764e5)
 + importlib-metadata==8.4.0
 + packaging==24.1
 + pyproject-hooks==1.1.0
 + tomli==2.0.1
 + zipp==3.20.1
```

例: `build` をアップデートする。

```sh
> uv pip install --upgrade build
Resolved 6 packages in 137ms
Prepared 1 package in 1ms
Uninstalled 1 package in 0.62ms
Installed 1 package in 7ms
 - build==1.2.1 (from git+https://github.com/pypa/build.git@1e67c062e9d1b1a6d5ffed621f4b29902bb764e5)
 + build==1.2.2
```

例: [sampleproject](https://github.com/pypa/sampleproject) を編集可能な状態でインストールする。

```sh
> uv pip install -e .
Resolved 2 packages in 501ms
   Built sampleproject @ file:///tmp/sampleproject
Prepared 2 packages in 1.15s
Installed 2 packages in 6ms
 + peppercorn==0.6
 + sampleproject==3.0.0 (from file:///tmp/sampleproject)
```

## uninstall

パッケージをアンインストールする。
パッケージは *requrements file* も指定できる。

```sh
> uv pip uninstall <package>
> uv pip uninstall -r <requrements file>
```

依存関係にあるパッケージには影響しない。

例: `build` をアンインストールする。

```sh
> uv pip uninstall build
Uninstalled 1 package in 0.98ms
 - build==1.2.2
```

## freeze

インストールされたパッケージから *requrements file* を生成する。

```sh
> uv pip freeze
```

例: `build` をインストールした状態で *requrements file* を生成する。

```sh
> uv pip freeze
build==1.2.2
importlib-metadata==8.4.0
packaging==24.1
pyproject-hooks==1.1.0
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
    "name": "importlib-metadata",
    "version": "8.4.0"
  },
  {
    "name": "packaging",
    "version": "24.1"
  },
  {
    "name": "pyproject-hooks",
    "version": "1.1.0"
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
> uv pip show <package>
```

例: `build` の情報を表示する。

```sh
> uv pip show build
Name: build
Version: 1.2.2
Location: /tmp/sampleproject/.venv/lib/python3.9/site-packages
Requires: importlib-metadata, packaging, pyproject-hooks, tomli
Required-by:
```

## tree

環境のパッケージの依存関係を表示する。

```sh
> uv pip tree
```

例: `build` をインストールした状態で表示する。

```sh
> uv pip tree
build v1.2.2
├─ packaging v24.1
├─ pyproject-hooks v1.1.0
├─ importlib-metadata v8.4.0
│  └── mqq zipp v3.20.1
└─ tomli v2.0.1
```

## check

インストールされたパッケージの依存関係を検証する。
