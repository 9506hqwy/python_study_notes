# pytest

## テスト実行

`assert` 文で検査する。

```python
# test_main.py
def test_success():
    assert 3 == 1 + 2

def test_failure():
    assert 2 == 1 + 2
```

実行する。`test_XXX` モジュールの `text_XXX` メソッドが実行される。

```sh
> pytest
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 2 items

test_main.py .F                                                                                                               [100%]

============================================================= FAILURES ==============================================================
___________________________________________________________ test_failure ____________________________________________________________

    def test_failure():
>       assert 2 == 1 + 2
E       assert 2 == (1 + 2)

test_main.py:5: AssertionError
====================================================== short test summary info ======================================================
FAILED test_main.py::test_failure - assert 2 == (1 + 2)
==================================================== 1 failed, 1 passed in 0.01s ====================================================
```

クラスでグルーピングできる。

```python
# test_main.py
import pytest

class TestClass:
    def test_success(self):
        assert 3 == 1 + 2

    def test_failure(self):
        assert 2 == 1 + 2
```

実行する。`TestXXX` クラスの `test_XXX` メソッドが実行される。

```sh
> pytest
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 2 items

test_main.py .F                                                                                                               [100%]

============================================================= FAILURES ==============================================================
______________________________________________________ TestClass.test_failure _______________________________________________________

self = <test_main.TestClass object at 0x7f11254f1950>

    def test_failure(self):
>       assert 2 == 1 + 2
E       assert 2 == (1 + 2)

test_main.py:8: AssertionError
====================================================== short test summary info ======================================================
FAILED test_main.py::TestClass::test_failure - assert 2 == (1 + 2)
==================================================== 1 failed, 1 passed in 0.01s ====================================================
```

テストごとに実行する。

```sh
> pytest test_main.py::TestClass::test_failure
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 1 item

test_main.py F                                                                                                                [100%]

============================================================= FAILURES ==============================================================
______________________________________________________ TestClass.test_failure _______________________________________________________

self = <test_main.TestClass object at 0x7f1fae7c9810>

    def test_failure(self):
>       assert 2 == 1 + 2
E       assert 2 == (1 + 2)

test_main.py:8: AssertionError
====================================================== short test summary info ======================================================
FAILED test_main.py::TestClass::test_failure - assert 2 == (1 + 2)
========================================================= 1 failed in 0.01s =========================================================
```

条件に一致したテストを実行する。

```sh
> pytest -k "Class and failure"
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 2 items / 1 deselected / 1 selected

test_main.py F                                                                                                                [100%]

============================================================= FAILURES ==============================================================
______________________________________________________ TestClass.test_failure _______________________________________________________

self = <test_main.TestClass object at 0x7fe9f07ad950>

    def test_failure(self):
>       assert 2 == 1 + 2
E       assert 2 == (1 + 2)

test_main.py:8: AssertionError
====================================================== short test summary info ======================================================
FAILED test_main.py::TestClass::test_failure - assert 2 == (1 + 2)
================================================== 1 failed, 1 deselected in 0.01s ==================================================
```

## テスト構成

### fixtures

pytest のビルトイン fixtures は `pytest --fixtures` で確認できる。
各テスト前に処理を実行して引数に渡される。

```python
# test_main.py
def test_success(tmp_path):
    assert "" != tmp_path
```

実行する。

```sh
> pytest
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 1 item

test_main.py .                                                                                                                [100%]

========================================================= 1 passed in 0.01s =========================================================
```

fixtures を実装する。

```python
# test_main.py
import pytest


@pytest.fixture
def one():
    return 1


def test_success(one):
    assert 1 == one
```

実行する。

```sh
> pytest
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 1 item

test_main.py .                                                                                                                [100%]

========================================================= 1 passed in 0.00s =========================================================
```

### `setUp` と `tearDown`

`unittest` の `setUp` と `tearDown` は fixtures で実現する。

```python
# test_main.py
import pytest


@pytest.fixture
def upDown():
    setUp()
    yield
    tearDown()


def setUp():
    print("setUp")


def tearDown():
    print("tearDown")


def test_success(upDown):
    print("test_success")
    assert 3 == 1 + 2


def test_failure(upDown):
    print("test_failure")
    assert 2 == 1 + 2
```

実行する。

```sh
> pytest -s
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 2 items

test_main.py setUp
test_success
.tearDown
setUp
test_failure
FtearDown


============================================================= FAILURES ==============================================================
___________________________________________________________ test_failure ____________________________________________________________

upDown = None

    def test_failure(upDown):
        print("test_failure")
>       assert 2 == 1 + 2
E       assert 2 == (1 + 2)

test_main.py:26: AssertionError
====================================================== short test summary info ======================================================
FAILED test_main.py::test_failure - assert 2 == (1 + 2)
==================================================== 1 failed, 1 passed in 0.01s ====================================================
```

fixtures オブジェクトを参照しない場合は `usefixtures` を利用できる。
`@pytest.fixture()` の `autouse` を `True` に設定するとデコレートせずに実行できる。

```python
# test_main.py
import pytest


@pytest.fixture
def upDown():
    setUp()
    yield
    tearDown()


def setUp():
    print("setUp")


def tearDown():
    print("tearDown")


@pytest.mark.usefixtures("upDown")
def test_success():
    print("test_success")
    assert 3 == 1 + 2


@pytest.mark.usefixtures("upDown")
def test_failure():
    print("test_failure")
    assert 2 == 1 + 2
```

実行する。

```sh
> pytest -s
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 2 items

test_main.py setUp
test_success
.tearDown
setUp
test_failure
FtearDown


============================================================= FAILURES ==============================================================
___________________________________________________________ test_failure ____________________________________________________________

    @pytest.mark.usefixtures("upDown")
    def test_failure():
        print("test_failure")
>       assert 2 == 1 + 2
E       assert 2 == (1 + 2)

test_main.py:28: AssertionError
====================================================== short test summary info ======================================================
FAILED test_main.py::test_failure - assert 2 == (1 + 2)
==================================================== 1 failed, 1 passed in 0.01s ====================================================
```

### `setUpClass` と `tearDownClass`

`unittest` の `setUpClass` と `tearDownClass` は `class` スコープの fixtures で実現する。

```python
# test_main.py
import pytest


@pytest.fixture(scope="class")
def upDown():
    setUpClass()
    yield
    tearDownClass()


def setUpClass():
    print("setUpClass")


def tearDownClass():
    print("tearDownClass")


@pytest.mark.usefixtures("upDown")
class TestClass:
    def test_success(self):
        print("test_success")
        assert 3 == 1 + 2


    def test_failure(self):
        print("test_failure")
        assert 2 == 1 + 2
```

実行する。

```sh
> pytest -s
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 2 items

test_main.py setUpClass
test_success
.test_failure
FtearDownClass


============================================================= FAILURES ==============================================================
______________________________________________________ TestClass.test_failure _______________________________________________________

self = <test_main.TestClass object at 0x7f86e04e1450>

    def test_failure(self):
        print("test_failure")
>       assert 2 == 1 + 2
E       assert 2 == (1 + 2)

test_main.py:28: AssertionError
====================================================== short test summary info ======================================================
FAILED test_main.py::TestClass::test_failure - assert 2 == (1 + 2)
==================================================== 1 failed, 1 passed in 0.01s ====================================================
```

### 非同期テスト

pytest-asyncio をインストールする。

```sh
> pip install pytest-asyncio
```

`@pytest.mark.asyncio` デコレータを使用する。

```python
# test_main.py
import pytest


@pytest.fixture
def upDown():
    setUp()
    yield
    tearDown()


def setUp():
    print("setUp")


def tearDown():
    print("tearDown")


@pytest.mark.asyncio
@pytest.mark.usefixtures("upDown")
async def test_success():
    print("test_success")
    assert 3 == 1 + 2


@pytest.mark.asyncio
@pytest.mark.usefixtures("upDown")
async def test_failure():
    print("test_failure")
    assert 2 == 1 + 2
```

実行する。

```sh
> pytest -s
======================================================================== test session starts ========================================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
plugins: asyncio-0.25.3
asyncio: mode=Mode.STRICT, asyncio_default_fixture_loop_scope=None
collected 2 items

test_main.py setUp
test_success
.tearDown
setUp
test_failure
FtearDown


============================================================================= FAILURES ==============================================================================
___________________________________________________________________________ test_failure ____________________________________________________________________________

    @pytest.mark.asyncio
    @pytest.mark.usefixtures("upDown")
    async def test_failure():
        print("test_failure")
>       assert 2 == 1 + 2
E       assert 2 == (1 + 2)

test_main.py:31: AssertionError
====================================================================== short test summary info ======================================================================
FAILED test_main.py::test_failure - assert 2 == (1 + 2)
==================================================================== 1 failed, 1 passed in 0.03s ====================================================================
```

## テスト処理

`assert` 文で検査する。

### 例外

`pytest.raises()` で例外を期待する。

```python
# test_main.py
import pytest


def test_error():
    with pytest.raises(ZeroDivisionError):
        a = 1 / 0
```

実行する。

```sh
> pytest
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 1 item

test_main.py .                                                                                                                [100%]

========================================================= 1 passed in 0.00s ========================================================
```

### 失敗

失敗を期待する。

```python
# test_main.py
import pytest


class TestClass:
    @pytest.mark.xfail(reason="@xfail")
    def test_failure1(self):
        assert False

    @pytest.mark.xfail(condition=1 < 2, reason="@xfail")
    def test_failure2(self):
        assert False
```

実行する。

```sh
> pytest
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 2 items

test_main.py xx                                                                                                               [100%]

======================================================== 2 xfailed in 0.01s =========================================================
```

### スキップ

テストをスキップする。

```python
# test_main.py
import pytest


class TestClass:
    @pytest.mark.skip(reason="@skip")
    def test_failure1(self):
        assert False

    @pytest.mark.skipif(1 < 2, reason="@skipif")
    def test_failure2(self):
        assert False

    def test_failure3(self):
        pytest.skip(reason="skipTest")
        assert False
```

実行する。

```sh
> pytest
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 3 items

test_main.py sss                                                                                                              [100%]

======================================================== 3 skipped in 0.01s =========================================================
```

## パッチ

fixtures の `monkeypatch` を使用する。

```python
# test_main.py
import pytest


def add(a: int, b: int) -> int:
    raise NotImplementedError()


class TestClass:
    def test_success(self, monkeypatch):
        monkeypatch.setattr('test_main.add', lambda *args: 3)

        assert 3 == add(1, 2)

    def test_failure(self, monkeypatch):
        monkeypatch.setattr('test_main.add', lambda *args: 2)

        assert 3 == add(1, 2)
```

実行する。

```sh
> pytest
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 2 items

test_main.py .F                                                                                                               [100%]

============================================================= FAILURES ==============================================================
______________________________________________________ TestClass.test_failure _______________________________________________________

self = <test_main.TestClass object at 0x7f62b7eb9950>, monkeypatch = <_pytest.monkeypatch.MonkeyPatch object at 0x7f62b7e78e90>

    def test_failure(self, monkeypatch):
        monkeypatch.setattr('test_main.add', lambda *args: 2)

>       assert 3 == add(1, 2)
E       assert 3 == 2
E        +  where 2 = add(1, 2)

test_main.py:18: AssertionError
====================================================== short test summary info ======================================================
FAILED test_main.py::TestClass::test_failure - assert 3 == 2
==================================================== 1 failed, 1 passed in 0.01s ====================================================
```

モックとして `unittest.mock` が使用できる。

```python
# test_main.py
from unittest.mock import MagicMock
import pytest


def add(a: int, b: int) -> int:
    raise NotImplementedError()


class TestClass:
    def test_success(self, monkeypatch):
        m = MagicMock(return_value=3)
        monkeypatch.setattr('test_main.add', m)

        assert 3 == add(1, 2)
        m.assert_called_once_with(1, 2)

    def test_failure(self, monkeypatch):
        m = MagicMock(return_value=2)
        monkeypatch.setattr('test_main.add', m)

        assert 3 == add(1, 2)
        m.assert_called_once_with(1, 2)
```

実行する。

```sh
> pytest
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 2 items

test_main.py .F                                                                                                               [100%]

============================================================= FAILURES ==============================================================
______________________________________________________ TestClass.test_failure _______________________________________________________

self = <test_main.TestClass object at 0x7f5206e84e10>, monkeypatch = <_pytest.monkeypatch.MonkeyPatch object at 0x7f5206e809d0>

    def test_failure(self, monkeypatch):
        m = MagicMock(return_value=2)
        monkeypatch.setattr('test_main.add', m)

>       assert 3 == add(1, 2)
E       assert 3 == 2
E        +  where 2 = add(1, 2)

test_main.py:22: AssertionError
====================================================== short test summary info ======================================================
FAILED test_main.py::TestClass::test_failure - assert 3 == 2
==================================================== 1 failed, 1 passed in 0.04s ====================================================
```

環境変数にパッチする。

```python
# test_main.py
import os
import pytest


class TestClass:
    def test_success(self, monkeypatch):
        monkeypatch.setenv("test", "a")

        assert "a" == os.getenv("test")
```

実行する。

```sh
> pytest
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 1 item

test_main.py .                                                                                                                [100%]

========================================================= 1 passed in 0.00s =========================================================
```

コンテキストマネージャを使用することもできる。

```python
# test_main.py
import os
import pytest


class TestClass:
    def test_success(self, monkeypatch):
        with monkeypatch.context() as m:
            m.setenv("test", "a")

            assert "a" == os.getenv("test")
```

実行する。

```sh
> pytest
======================================================== test session starts ========================================================
platform linux -- Python 3.13.2, pytest-8.3.4, pluggy-1.5.0
rootdir: /root/workspace
collected 1 item

test_main.py .                                                                                                                [100%]

========================================================= 1 passed in 0.00s =========================================================
```
