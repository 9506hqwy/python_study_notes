# logging

Python 標準のロギングモジュールである。

メッセージは `logging.LogRecord` にインスタンス化されロガーに設定されたハンドラに渡される。

ロガーは名前で親子関係を形成し、親ロガーを含めてすべてのロガーに設定されたハンドラにメッセージが渡り、
ハンドラに応じた処理が行われる。

ロガー、ハンドラにはそれぞれにログレベルやフィルタが設定されメッセージを処理するかどうかを決定する。
ロガーのログレベルは親ロガーのログレベルが継承される。

## ロガー

既定の設定でメッセージを出力する。

```python
# main.py
import logging
logging.warning("w")
logging.info("i")
```

既定で `WARNING` 以上のメッセージが `ログレベル:ロガー名:メッセージ` の形式で出力される。

`logging.warning()` や `logging.warning()` を呼び出すことで　`logging.basicConfig()` が実行される。
既定で名前が `root` のルートロガーが作成され、ハンドラが `logging.StreamHandler` が設定される。

```sh
> python main.py
WARNING:root:w
```

設定を変更する。
`logging.warning()` や `logging.warning()` を呼び出す前に `logging.basicConfig()` で設定する。

```python
# main.py
import logging
logging.basicConfig(level=logging.INFO)
logging.warning("w")
logging.info("i")
```

`INFO` 以上のメッセージが出力される。

```sh
> python main.py
WARNING:root:w
INFO:root:i
```

ハンドラが設定されていない場合は `logging.lastResort` ハンドラに処理される。

```python
# main.py
import logging
logger = logging.getLogger(__name__)
logger.warning("w")
```

実行するとメッセージのみ出力される。

```sh
> python main.py
w
```

`logging.lastResort` を `None` に設定することで出力を抑制できる。

```python
# main.py
import logging
logging.lastResort = None
logger = logging.getLogger(__name__)
logger.warning("w")
```

実行するとメッセージのみ出力される。

```sh
> python main.py
No handlers could be found for logger "__main__"
```

### 例外の情報

メッセージとあわせて例外とスタックトレースの情報を出力する。

```python
# main.py
import logging

try:
    raise Exception("Message.")
except Exception as e:
    print("logging.error: -------------------------------------")
    logging.error(f"{e!s}")

    print("logging.error: exc_info=True ----------------------")
    logging.error(f"{e!s}", exc_info=True)

    print("logging.error: exc_info=True stack_info=True ------")
    logging.error(f"{e!s}", exc_info=True, stack_info=True)

    print("logging.exception: ---------------------------------")
    logging.exception(f"{e!s}")
```

`exc_info=True` と `logging.exception()` は同じ出力となる。

```sh
> python main.py
logging.error: -------------------------------------
ERROR:root:Message.
logging.error: exc_info=True ----------------------
ERROR:root:Message.
Traceback (most recent call last):
  File "/root/workspace/main.py", line 5, in <module>
    raise Exception("Message.")
Exception: Message.
logging.error: exc_info=True stack_info=True ------
ERROR:root:Message.
Traceback (most recent call last):
  File "/root/workspace/main.py", line 5, in <module>
    raise Exception("Message.")
Exception: Message.
Stack (most recent call last):
  File "/root/workspace/main.py", line 14, in <module>
    logging.error(f"{e!s}", exc_info=True, stack_info=True)
logging.exception: ---------------------------------
ERROR:root:Message.
Traceback (most recent call last):
  File "/root/workspace/main.py", line 5, in <module>
    raise Exception("Message.")
Exception: Message.
```

## フォーマット

出力の形式を変更する。
使用できる属性は [LogRecord 属性](https://docs.python.org/ja/3.13/library/logging.html#logrecord-attributes) を参照する。

フォーマットは `logging.BasicConfig()` やハンドラに設定する。

```python
# main.py
import logging
logging.basicConfig(format="%(asctime)s [%(levelname)s] %(message)s")
logging.warning("w")
```

実行する。

```sh
> python main.py
2025-03-01 04:39:13,459 [WARNING] w
```

### `logging.Formmater` クラス

`Formatter` クラスを利用して形式を変更する。

```python
# main.py
from datetime import datetime, timezone
import logging
import time


class Formatter(logging.Formatter):
    converter = time.gmtime

    def formatTime(self, record, datefmt=None):
        dt = datetime.fromtimestamp(record.created, tz=timezone.utc)
        return dt.isoformat()


logging.basicConfig(format="%(asctime)s [%(levelname)s] %(message)s")

stream = logging.StreamHandler()
stream.setFormatter(Formatter("%(asctime)s [%(levelname)s] %(message)s"))

logger = logging.getLogger(__name__)
logger.addHandler(stream)

logger.warning("w")
```

時刻を ISO8601 形式で出力する。

```sh
> python main.py
2025-03-01T05:50:17.306331+00:00 [WARNING] w
2025-03-01 05:50:17,306 [WARNING] w
```

## ハンドラ

メッセージを処理する。

```python
# main.py
import logging
logging.basicConfig(format=f"{logging.BASIC_FORMAT}:root")

stream = logging.StreamHandler()
stream.setFormatter(logging.Formatter(f"{logging.BASIC_FORMAT}:{__name__}"))

logger = logging.getLogger(__name__)
logger.addHandler(stream)

logging.warning("w1")
logger.warning("w2")
```

メッセージ `w1` はルートロガーのハンドラ、メッセージ `w2` はルートロガーと `__main__` のロガーのハンドラで処理される。

```sh
> python main.py
WARNING:root:w1:root
WARNING:__main__:w2:__main__
WARNING:__main__:w2:root
```

`logging.NullHandler` で何もしないハンドラを登録できる。

```python
# main.py
import logging
logging.basicConfig(handlers=[logging.NullHandler()])

stream = logging.StreamHandler()
stream.setFormatter(logging.Formatter(f"{logging.BASIC_FORMAT}:{__name__}"))

logger = logging.getLogger(__name__)
logger.addHandler(stream)

logging.warning("w1")
logger.warning("w2")
```

ルートロガーは何も出力しない。

```sh
> python main.py
WARNING:__main__:w2:__main__
```

## レコード

`logging.LogRecord` を拡張してメッセージに出力する情報を追加する。

```python
# main.py
import logging

logging_factory = logging.getLogRecordFactory()


def factory(*args, **kwargs):
    record = logging_factory(*args, **kwargs)
    record.custom_id = 1
    return record


logging.basicConfig(format="%(asctime)s [%(levelname)s] %(custom_id)d %(message)s")
logging.setLogRecordFactory(factory)

logging.warning("w")
```

`LogRecord` 属性はフォーマットを指定して出力することができる。

```sh
# python main.py
2025-03-01 06:00:59,359 [WARNING] 1 w
```
