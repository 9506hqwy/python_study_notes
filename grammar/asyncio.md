# 非同期 I/O

I/O バウンドな処理をコルーチンを利用して非同期に並行実行する。

REPL で `asyncio` を使用する場合は引数に `-m asyncio` を指定して起動する。

```sh
> python -m asyncio
asyncio REPL 3.13.2 (main, Feb 25 2025, 05:25:21) [GCC 12.2.0] on linux
Use "await" directly instead of "asyncio.run()".
Type "help", "copyright", "credits" or "license" for more information.
>>> import asyncio
>>>
```

## 非同期処理

コルーチンを利用する処理を `await` する。
同期処理から `asyncio.run()` を使用してコルーチンを利用した処理をイベントループに渡して実行する。

```python
# main.py
import asyncio
import threading
from datetime import datetime


async def main():
    print(f"{datetime.now()} : {threading.get_ident()}")
    await asyncio.sleep(1)
    print(f"{datetime.now()} : {threading.get_ident()}")


if __name__ == '__main__':
    asyncio.run(main())
```

`asyncio.run()` で新規のイベントループを作成し非同期処理を実行する。
処理が完了するとイベントループは閉じる。

```sh
> python main.py
2025-03-02 01:20:50.323748 : 140650681604992
2025-03-02 01:20:51.325277 : 140650681604992
```

### 同期処理

非同期処理から同期処理を実行する。

```python
# main.py
import asyncio
import threading
import time
from datetime import datetime


def sleep(secs):
    print(f"{datetime.now()} : {threading.get_ident()}")
    time.sleep(secs)
    print(f"{datetime.now()} : {threading.get_ident()}")


async def main():
    print(f"{datetime.now()} : {threading.get_ident()}")
    await asyncio.to_thread(sleep, 1)
    print(f"{datetime.now()} : {threading.get_ident()}")


if __name__ == '__main__':
    asyncio.run(main())
```

別スレッドで実行される。

```sh
> python main.py
2025-03-02 03:46:21.130626 : 140636718615424
2025-03-02 03:46:21.131401 : 140636703282880
2025-03-02 03:46:22.131878 : 140636703282880
2025-03-02 03:46:22.132453 : 140636718615424
```

## タスク

イベントループで処理するときにコルーチンはタスクにラップされる。
`asyncio.create_task()` でコルーチンをタスクにラップする。
タスクはコルーチンの状態取得やキャンセル操作ができる。

```python
# main.py
import asyncio
import threading
from datetime import datetime


async def sleep():
    print(f"{datetime.now()} : {threading.get_ident()}")
    await asyncio.sleep(3)
    print(f"{datetime.now()} : {threading.get_ident()}")


async def main():
    task = asyncio.create_task(sleep())

    await asyncio.sleep(1)
    task.cancel()

    try:
        result = await task
    except asyncio.CancelledError as e:
        print(f"cancel? : {task.cancelled()}")


if __name__ == '__main__':
    asyncio.run(main())
```

キャンセルすると `await` 時に `asyncio.CancelledError` が発生する。

```sh
# python main.py
2025-03-02 01:46:05.487435 : 140330964855680
cancel? : True
```

タスクをキャンセルできないようにするには `asyncio.shield()` を使用する。
ただし、`await` しているコルーチンがキャンセルされた場合はキャンセルされる。

```python
# main.py
import asyncio
import threading
from datetime import datetime


async def sleep(secs):
    print(f"{datetime.now()} : {threading.get_ident()}")
    await asyncio.sleep(secs)
    print(f"{datetime.now()} : {threading.get_ident()}")


async def main():
    task = asyncio.create_task(sleep(2))
    task = asyncio.shield(task)

    await asyncio.sleep(1)
    task.cancel()

    try:
        await task
    except asyncio.CancelledError:
        pass

    print(f"{datetime.now()} : {threading.get_ident()}")
    await asyncio.sleep(2)


if __name__ == '__main__':
    asyncio.run(main())
```

`asyncio.CancelledError` は発生するが `sleep` は継続される。

```sh
> python main.py
2025-03-02 03:01:54.779857 : 140673420655488
2025-03-02 03:01:55.781323 : 140673420655488
2025-03-02 03:01:56.782090 : 140673420655488
```

### タスクの取得

コルーチン処理の中からイベントループのタスクを取得する。

```python
# main.py
import asyncio
import threading
from datetime import datetime


async def main():
    print(f"{datetime.now()} : {threading.get_ident()}")
    print(asyncio.current_task())
    print(asyncio.all_tasks())
    print(f"{datetime.now()} : {threading.get_ident()}")


if __name__ == '__main__':
    asyncio.run(main())
```

イベントループで実行中のタスクと、未完了のタスクが取得できる。

```sh
> python main.py
2025-03-02 03:51:05.175286 : 140566884932480
<Task pending name='Task-1' coro=<main() running at /root/workspace/main.py:9> cb=[_run_until_complete_cb() at /usr/local/lib/python3.13/asyncio/base_events.py:181]>
{<Task pending name='Task-1' coro=<main() running at /root/workspace/main.py:10> cb=[_run_until_complete_cb() at /usr/local/lib/python3.13/asyncio/base_events.py:181]>}
2025-03-02 03:51:05.175368 : 140566884932480
```

### 並行処理

`TaskGroup` を使用して複数のタスクをまとめて処理する。

```python
# main.py
import asyncio
import threading
from datetime import datetime


async def sleep(secs):
    print(f"{datetime.now()} : {threading.get_ident()}")
    await asyncio.sleep(secs)
    print(f"{datetime.now()} : {threading.get_ident()}")


async def main():
    async with asyncio.TaskGroup() as g:
        g.create_task(sleep(1))
        g.create_task(sleep(2))


if __name__ == '__main__':
    asyncio.run(main())
```

実行する。

```sh
> python main.py
2025-03-02 01:53:41.162636 : 140086309890944
2025-03-02 01:53:41.162668 : 140086309890944
2025-03-02 01:53:42.164190 : 140086309890944
2025-03-02 01:53:43.164762 : 140086309890944
```

同様の処理を `asyncio.gather` で実行できる。
`TaskGroup` と異なるのは 1 つのコルーチンで例外が発生した場合に他のコルーチンは継続される。

```python
# main.py
import asyncio
import threading
from datetime import datetime


class TerminateException(Exception):
    pass


async def sleep(secs):
    print(f"{datetime.now()} : {threading.get_ident()}")
    await asyncio.sleep(secs)
    print(f"{datetime.now()} : {threading.get_ident()}")
    raise TerminateException(secs)


async def main():
    try:
        async with asyncio.TaskGroup() as g:
            t1 = g.create_task(sleep(1))
            t2 = g.create_task(sleep(2))
    except* TerminateException as e:
        print(e)

    print(f"t1 canceled? : {t1.cancelled()}")
    print(f"t2 canceled? : {t2.cancelled()}")


if __name__ == '__main__':
    asyncio.run(main())
```

コンテキストマネージャを抜けるときにタスクはキャンセルされ 2 つめのタスクが実行されない。

```sh
> python main.py
2025-03-02 02:28:38.181265 : 140565804915584
2025-03-02 02:28:38.181298 : 140565804915584
2025-03-02 02:28:39.182729 : 140565804915584
unhandled errors in a TaskGroup (1 sub-exception)
t1 canceled? : False
t2 canceled? : True
```

`asyncio.gather()` で例外を発生させないようにするには `return_exceptions` を `True` に設定する。

```python
# main.py
import asyncio
import threading
from datetime import datetime


class TerminateException(Exception):
    pass


async def sleep(secs):
    print(f"{datetime.now()} : {threading.get_ident()}")
    await asyncio.sleep(secs)
    print(f"{datetime.now()} : {threading.get_ident()}")
    raise TerminateException(secs)


async def main():
    t1 = asyncio.create_task(sleep(3))
    t2 = asyncio.create_task(sleep(2))

    try:
        await asyncio.gather(t1, t2)
    except* TerminateException as e:
        print(e)

    await asyncio.sleep(2)

    print(f"t1 canceled? : {t1.cancelled()}")
    print(f"t2 canceled? : {t2.cancelled()}")


if __name__ == '__main__':
    asyncio.run(main())
```

例外が発生した後もタスクは実行される。

```sh
> python main.py
2025-03-02 02:26:39.815452 : 140576014633856
2025-03-02 02:26:39.815488 : 140576014633856
2025-03-02 02:26:41.818032 : 140576014633856
 (1 sub-exception)
2025-03-02 02:26:42.817729 : 140576014633856
t1 canceled? : False
t2 canceled? : False
```

## サブプロセス

サブプロセスを非同期に実行する。

```python
# main.py
import asyncio
import threading
from datetime import datetime


async def main():
    print(f"{datetime.now()} : {threading.get_ident()}")
    p = await asyncio.create_subprocess_shell("sleep 1")
    await p.wait()
    print(f"{datetime.now()} : {threading.get_ident()}")


if __name__ == '__main__':
    asyncio.run(main())
```

プロセスを生成し完了を待つ。

```sh
> python main.py
2025-03-02 04:13:37.571867 : 139949553523584
2025-03-02 04:13:38.574643 : 139949553523584
```
