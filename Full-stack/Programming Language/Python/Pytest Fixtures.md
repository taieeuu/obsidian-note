---
Date: 2024-09-21
---
### 1. 什麼是 Fixtures？
- **介紹**：簡介 `pytest` 中的 Fixtures 概念，解釋其用途——用於在測試之前準備測試環境或數據，以及在測試之後進行清理。
- **優點**：自動化測試環境準備、重複使用、提高測試效率。
### 2. 定義 Fixtures
- **基本用法**：介紹如何使用 `@pytest.fixture` 定義一個 Fixture。

```python
@pytest.fixture
def setup_data():
    return {"name": "John", "age": 30}

def test_example(setup_data):
    assert setup_data["name"] == "John"
```
### 3. Fixture 的作用域（Scope）
- **作用域的選擇**：介紹不同作用域的 Fixture（`function`、`class`、`module`、`session`）及其應用場景。
    - `function`: 每個測試函數執行前都會調用一次（默認）。
    - `class`: 每個測試類別執行前調用一次。
    - `module`: 每個模組執行前調用一次。
    - `session`: 整個測試會話期間只調用一次。
      
```python
@pytest.fixture(scope="module")
def setup_module_data():
    return {"name": "ModuleData"}
```
### 4. 自動調用 Fixtures
- **自動使用**：解釋如何使用 `autouse=True` 讓某些 Fixture 自動調用，而無需在測試中顯式傳遞。
  
```python
@pytest.fixture(autouse=True)
def setup_env():
    print("Setting up environment")
```
### 5. 使用 `yield` 清理資源
- **前後設置與清理**：展示如何使用 `yield` 來在測試結束後進行資源清理，類似 `try-finally` 構造。

```python
@pytest.fixture
def setup_teardown():
    # 測試前設置
    resource = open("file.txt", "w")
    yield resource
    # 測試後清理
    resource.close()
```
### 6. Fixtures 之間的相依性（依賴注入）
- **Fixture 的相互調用**：介紹如何讓一個 Fixture 依賴於另一個 Fixture，實現依賴注入。

```python
@pytest.fixture
def db_connection():
    return "Database Connection"

@pytest.fixture
def setup_user(db_connection):
    return f"User setup with {db_connection}"

def test_user(setup_user):
    assert "Database Connection" in setup_user
```
### 7. 參數化 Fixtures
- **傳遞參數給 Fixtures**：展示如何通過 `params` 參數來使 Fixtures 參數化，適用於多組輸入進行測試。

```python
@pytest.fixture(params=[0, 1, 2])
def number(request):
    return request.param

def test_numbers(number):
    assert number < 3
```
### 8. 使用 `conftest.py` 文件共享 Fixtures
- **`conftest.py` 介紹**：解釋如何通過 `conftest.py` 文件來定義全局可用的 Fixtures，這些 Fixtures 無需顯式導入即可在項目的多個測試文件中使用。

```python
# conftest.py
@pytest.fixture
def setup_global_data():
    return {"name": "Global"}
```
### 9. 模擬與補丁（Mocking and Patching）
- **使用 Fixtures 模擬資源**：介紹如何使用 `pytest` 與 `unittest.mock` 或 `pytest-mock` 插件進行模擬，為測試提供假資源。
  
```python
from unittest.mock import patch

@pytest.fixture
def mock_external_api():
    with patch('module.external_api') as mock_api:
        mock_api.return_value = {"status": "success"}
        yield mock_api
```

**補充**
- `module.external_api` : 這是一個 func 所在位置。
###### 範例如下：
module.py
```python
# 這是一個外部 API 調用，可能會對網絡發送請求
def external_api():
    response = requests.get("https://api.example.com/data")
    return response.json()
```

測試文件（例如 `test_module.py`）
```python
from unittest.mock import patch
import pytest
from module import external_api

@pytest.fixture
def mock_external_api():
    # 模擬 external_api 函數，使其返回一個預設的結果
    with patch('module.external_api') as mock_api:
        mock_api.return_value = {"status": "success"}
        yield mock_api

def test_api_call(mock_external_api):
    # 測試過程中，external_api 函數不會真正發出網絡請求，而是返回模擬數據
    result = external_api()
    assert result == {"status": "success"}
```
### 注意
- **Fixture 命名與重用**：討論如何命名 Fixtures，讓它們在多個測試中重複使用。
- **避免過度使用**：介紹如何避免過度使用 Fixtures，保持測試的簡單性與可維護性。
- **Scope 的合理使用**：如何在適當的情況下選擇合適的作用域來提高測試性能。