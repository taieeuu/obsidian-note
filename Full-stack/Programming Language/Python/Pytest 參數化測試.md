---
Date: 2024-09-21
---
### 1. 參數化測試的介紹
- **介紹**：說明參數化測試的基本概念，讓一個測試函數接受多組輸入，並執行多次測試。
- **優點**：提高測試覆蓋率，減少重複代碼，適合多組數據測試相同行為。
### 2. `@pytest.mark.parametrize` 的使用
- **基本語法**：介紹如何使用 `@pytest.mark.parametrize` 修飾符來傳遞多組參數。

```python
@pytest.mark.parametrize("input, expected", [
    (1, 2),
    (2, 3),
    (3, 4)
])
def test_increment(input, expected):
    assert input + 1 == expected
```

- **多參數傳遞**：如何傳遞多個參數組進行測試。

```python
@pytest.mark.parametrize("a, b, result", [
    (1, 2, 3),
    (2, 3, 5),
    (3, 5, 8)
])
def test_addition(a, b, result):
    assert a + b == result
```
### 3. 測試名稱生成
- **自訂測試名稱**：使用 `ids` 參數來自定義每組測試的名稱，讓測試報告更具可讀性。

```python
@pytest.mark.parametrize("input, expected", [
    (1, 2),
    (2, 3),
    (3, 4)
], ids=["test_case_1", "test_case_2", "test_case_3"])
def test_increment(input, expected):
    assert input + 1 == expected
```
### 4. 傳遞複雜的參數類型
- **傳遞字典、列表等複雜數據**：展示如何將複雜的數據結構作為測試參數。
  
```python
@pytest.mark.parametrize("data, expected", [
    ({"name": "John", "age": 25}, "John"),
    ({"name": "Jane", "age": 30}, "Jane")
])
def test_name(data, expected):
    assert data["name"] == expected
```
### 5. Fixtures 與參數化測試的結合
- **參數化 Fixtures**：介紹如何將 Fixtures 與參數化測試結合使用，使 Fixtures 能夠接受多組參數。

```python
@pytest.fixture(params=[0, 1, 2])
def number(request):
    return request.param

def test_numbers(number):
    assert number < 3
```
### 6. 測試數據與外部來源
- **從文件中讀取測試數據**：展示如何從 CSV、JSON 或其他文件中讀取測試數據，並將其參數化。

```python
import csv

@pytest.mark.parametrize("username, password", csv.reader(open('users.csv')))
def test_login(username, password):
    assert login(username, password)
```
### 7. 結合條件判斷的參數化測試
- **帶條件的參數化測試**：結合 `skipif` 等功能條件地跳過某些參數化的測試。

```python
@pytest.mark.parametrize("input, expected", [
    pytest.param(1, 2, marks=pytest.mark.skipif(sys.platform == "win32", reason="不支持 Windows")),
    (2, 3),
    (3, 4)
])
def test_increment(input, expected):
    assert input + 1 == expected
```