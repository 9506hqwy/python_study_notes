# scalene

CPU, GPU, メモリの統計的プロファイラになる。
行レベルで結果が表示される。

[redfish-service-py](https://github.com/9506hqwy/redfish-service-py) を使って確認する。

## インストール

インストールする。

```sh
uv pip install scalene
```

## プロファイル

プロファイラを有効にしてサービスを起動する。

```sh
uv run scalene --outfile profile.html --html --no-browser -m redfish_service
```

アクセス後にサービスを停止する。

```sh
curl -u admin:admin -i http://127.0.0.1:8000/redfish/v1/SessionService/Sessions
```

下記が出力される。

![scalene の可視化](../_static/image/scalene.png "scalene の可視化")
