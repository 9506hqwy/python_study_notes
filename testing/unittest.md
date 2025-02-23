# unittest

Python 標準のユニットテストモジュールである。

## テスト実行

`unittest.TestCase` を継承してテストを実装する。

```python
# main.py
import unittest


class TestClass(unittest.TestCase):
    def test_success(self):
        self.assertEqual(3, 1 + 2)

    def test_failure(self):
        self.assertEqual(2, 1 + 2)


if __name__ == "__main__":
    unittest.main()
```

実行する。`unittest.TestCase` を継承したクラスの `text_XXX` メソッドが実行される。

```sh
> python main.py
F.
======================================================================
FAIL: test_failure (__main__.TestClass.test_failure)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/root/workspace/main.py", line 8, in test_failure
    self.assertEqual(2, 1 + 2)
    ~~~~~~~~~~~~~~~~^^^^^^^^^^
AssertionError: 2 != 3

----------------------------------------------------------------------
Ran 2 tests in 0.000s

FAILED (failures=1)
```

テストごとに実行する。

```sh
> python -m unittest main.TestClass.test_failure
F
======================================================================
FAIL: test_failure (main.TestClass.test_failure)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/root/workspace/main.py", line 8, in test_failure
    self.assertEqual(2, 1 + 2)
    ~~~~~~~~~~~~~~~~^^^^^^^^^^
AssertionError: 2 != 3

----------------------------------------------------------------------
Ran 1 test in 0.000s

FAILED (failures=1)
```

## テスト構成

テストの実行前後に処理を実施する。

```python
# main.py
import unittest


class TestClass(unittest.TestCase):
    def setUp(self):
        print("setUp")

    def tearDown(self):
        print("tearDown")

    def test_success(self):
        print("test_success")
        self.assertEqual(3, 1 + 2)

    def test_failure(self):
        print("test_failure")
        self.assertEqual(2, 1 + 2)


if __name__ == "__main__":
    unittest.main()
```

実行する。
各テストの前に `setUp()` が実行され、後に `tearDown()` が実行される。

```sh
> python main.py
setUp
test_failure
FtearDown
setUp
test_success
tearDown
.
======================================================================
FAIL: test_failure (__main__.TestClass.test_failure)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/root/workspace/main.py", line 17, in test_failure
    self.assertEqual(2, 1 + 2)
    ~~~~~~~~~~~~~~~~^^^^^^^^^^
AssertionError: 2 != 3

----------------------------------------------------------------------
Ran 2 tests in 0.000s

FAILED (failures=1)
```

テストケースの実行前後に処理を実施する。

```python
# main.py
import unittest


class TestClass(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        print("setUpClass")

    @classmethod
    def tearDownClass(cls):
        print("tearDownClass")

    def test_success(self):
        print("test_success")
        self.assertEqual(3, 1 + 2)

    def test_failure(self):
        print("test_failure")
        self.assertEqual(2, 1 + 2)


if __name__ == "__main__":
    unittest.main()
```

実行する。
各テストケースの前に `setUpClass()` が実行され、後に `tearDownClass()` が実行される。

```sh
> python main.py
setUpClass
test_failure
Ftest_success
.tearDownClass

======================================================================
FAIL: test_failure (__main__.TestClass.test_failure)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/root/workspace/main.py", line 19, in test_failure
    self.assertEqual(2, 1 + 2)
    ~~~~~~~~~~~~~~~~^^^^^^^^^^
AssertionError: 2 != 3

----------------------------------------------------------------------
Ran 2 tests in 0.000s

FAILED (failures=1)
```

## テスト処理

`unittest.TestCase.assertXXX` 系メソッドで検査する。

### 例外

`assertRaises` で例外を期待する。

```python
# main.py
import unittest


class TestClass(unittest.TestCase):
    def test_error(self):
        with self.assertRaises(ZeroDivisionError):
            a = 1 / 0


if __name__ == "__main__":
    unittest.main()
```

実行する。

```sh
> python main.py
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```

### 失敗

`fail()` でテストを失敗させる。

```python
# main.py
import unittest


class TestClass(unittest.TestCase):
    def test_failure(self):
        self.fail()


if __name__ == "__main__":
    unittest.main()
```

実行する。

```sh
> python main.py
F
======================================================================
FAIL: test_failure (__main__.TestClass.test_failure)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/root/workspace/main.py", line 6, in test_failure
    self.fail()
    ~~~~~~~~~^^
AssertionError: None

----------------------------------------------------------------------
Ran 1 test in 0.000s

FAILED (failures=1)
```

失敗を期待する。

```python
# main.py
import unittest


class TestClass(unittest.TestCase):
    @unittest.expectedFailure
    def test_failure(self):
        self.fail()


if __name__ == "__main__":
    unittest.main()
```

実行する。

```sh
> python main.py
x
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK (expected failures=1)
```

### スキップ

テストをスキップさせる。

```python
# main.py
import unittest


class TestClass(unittest.TestCase):
    @unittest.skip("@skip")
    def test_failure1(self):
        self.fail()

    @unittest.skipIf(1 < 2, "@skipIf")
    def test_failure2(self):
        self.fail()

    @unittest.skipUnless(1 > 2, "@skipUnless")
    def test_failure3(self):
        self.fail()

    def test_failure4(self):
        self.skipTest("skipTest")
        self.fail()

    def test_failure5(self):
        raise unittest.SkipTest("SkipTest")
        self.fail()


if __name__ == "__main__":
    unittest.main()
```

実行する。

```sh
> python main.py
sssss
----------------------------------------------------------------------
Ran 5 tests in 0.000s

OK (skipped=5)
```

## モック

`unittest.mock` に `Mock` と `MagicMock` がある。
`MagicMock` は `Mock` の特殊メソッドが `MagicMock` で実装される。

`create_autospec()` を使用して既存のオブジェクトから `Mock` を作成することもできる。

```python
# main.py
import unittest
from unittest.mock import MagicMock


class TestClass(unittest.TestCase):
    def test_success(self):
        m = MagicMock(return_value=3)

        self.assertEqual(3, m(1, 2))
        m.assert_called_once_with(1, 2)

    def test_failure(self):
        m = MagicMock(return_value=3)

        self.assertEqual(2, m(1, 2))
        m.assert_called_once_with(1, 2)


if __name__ == "__main__":
    unittest.main()
```

実行する。
`Mock.assert_XXX` で呼び出し回数や引数を検証する。

```sh
> python main.py
F.
======================================================================
FAIL: test_failure (__main__.TestClass.test_failure)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/root/workspace/main.py", line 15, in test_failure
    self.assertEqual(2, m(1, 2))
    ~~~~~~~~~~~~~~~~^^^^^^^^^^^^
AssertionError: 2 != 3

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (failures=1)
```

## パッチ

モックを既存のオブジェクトと置換する。

```python
# main.py
import unittest
from unittest.mock import MagicMock, patch


def add(a: int, b: int) -> int:
    raise NotImplementedError()


class TestClass(unittest.TestCase):

    @patch("main.add")
    def test_success(self, m):
        m.return_value = 3

        self.assertEqual(3, m(1, 2))
        m.assert_called_once_with(1, 2)

    @patch("main.add")
    def test_failure(self, m):
        m.return_value = 3

        self.assertEqual(2, m(1, 2))
        m.assert_called_once_with(1, 2)


if __name__ == "__main__":
    unittest.main()
```

実行する。
デコレータでラップした順に `test_XXX()` の引数に追加されるため、
デコレータと引数の記述順とは逆になる。

```sh
> python main.py
F.
======================================================================
FAIL: test_failure (__main__.TestClass.test_failure)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/usr/local/lib/python3.13/unittest/mock.py", line 1424, in patched
    return func(*newargs, **newkeywargs)
  File "/root/workspace/main.py", line 22, in test_failure
    self.assertEqual(2, m(1, 2))
    ~~~~~~~~~~~~~~~~^^^^^^^^^^^^
AssertionError: 2 != 3

----------------------------------------------------------------------
Ran 2 tests in 0.002s

FAILED (failures=1)
```

デコレータはクラスに付与することもできる。

```python
# main.py
import unittest
from unittest.mock import MagicMock, patch


def add(a: int, b: int) -> int:
    raise NotImplementedError()


@patch("main.add")
class TestClass(unittest.TestCase):

    def test_success(self, m):
        m.return_value = 3

        self.assertEqual(3, m(1, 2))
        m.assert_called_once_with(1, 2)

    def test_failure(self, m):
        m.return_value = 3

        self.assertEqual(2, m(1, 2))
        m.assert_called_once_with(1, 2)


if __name__ == "__main__":
    unittest.main()
```

実行する。

```sh
> python main.py
F.
======================================================================
FAIL: test_failure (__main__.TestClass.test_failure)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/usr/local/lib/python3.13/unittest/mock.py", line 1424, in patched
    return func(*newargs, **newkeywargs)
  File "/root/workspace/main.py", line 21, in test_failure
    self.assertEqual(2, m(1, 2))
    ~~~~~~~~~~~~~~~~^^^^^^^^^^^^
AssertionError: 2 != 3

----------------------------------------------------------------------
Ran 2 tests in 0.002s

FAILED (failures=1)
```

コンテキストマネージャを使用することもできる。

```python
# main.py
import unittest
from unittest.mock import MagicMock, patch


def add(a: int, b: int) -> int:
    raise NotImplementedError()


class TestClass(unittest.TestCase):

    def test_success(self):
        with patch('main.add', return_value = 3) as m:
            self.assertEqual(3, m(1, 2))

        m.assert_called_once_with(1, 2)


if __name__ == "__main__":
    unittest.main()
```

実行する。

```sh
> python main.py
.
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
```

オブジェクトの定義から `Mock` を作成する。

```python
# main.py
import unittest
from unittest.mock import MagicMock, patch


def add(a: int, b: int) -> int:
    raise NotImplementedError()


class TestClass(unittest.TestCase):

    def test_success(self):
        with patch('main.add', autospec=True, return_value = 3) as m:
            self.assertEqual(3, m(1, 2))

        m.assert_called_once_with(1, 2)

    def test_error(self):
        with patch('main.add', autospec=True, return_value = 3) as m:
            self.assertEqual(3, m(1))

        m.assert_called_once_with(1, 2)


if __name__ == "__main__":
    unittest.main()
```

実行する。
シグネチャが合わない場合は例外は発生する。

```sh
> python main.py
E.
======================================================================
ERROR: test_error (__main__.TestClass.test_error)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/root/workspace/main.py", line 19, in test_error
    self.assertEqual(3, m(1))
                        ~^^^
  File "<string>", line 2, in add
  File "/usr/local/lib/python3.13/unittest/mock.py", line 194, in checksig
    sig.bind(*args, **kwargs)
    ~~~~~~~~^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.13/inspect.py", line 3264, in bind
    return self._bind(args, kwargs)
           ~~~~~~~~~~^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.13/inspect.py", line 3177, in _bind
    raise TypeError(msg) from None
TypeError: missing a required argument: 'b'

----------------------------------------------------------------------
Ran 2 tests in 0.003s

FAILED (errors=1)
```
