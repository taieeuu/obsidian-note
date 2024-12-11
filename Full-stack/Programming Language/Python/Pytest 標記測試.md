---
Date: 2024-09-21
---
### ### 1. `pytest` 測試標記（Markers）
- **介紹**：說明 `pytest` 提供的標記功能，用來對測試進行分組或做特定操作。
- **內置標記**：
    - `@pytest.mark.skip`: 跳過測試。
    - `@pytest.mark.skipif`: 在特定條件下跳過測試。
    - `@pytest.mark.xfail`: 預期測試失敗，不計入測試結果。
- **自定義標記**：如何使用 `pytest.ini` 或 `conftest.py` 創建自定義標記。
### 2. 跳過測試（Skipping Tests）
- **`@pytest.mark.skip` 使用**：展示如何使用 `skip` 標記直接跳過測試。

```python
# `reason` 參數的作用是提供一個簡短的說明，
@pytest.mark.skip(reason="暫時不需要測試此功能")
def test_example():
    assert 1 == 1
```

- **`@pytest.mark.skipif` 條件性跳過**：根據條件跳過測試，如在特定 Python 版本下跳過測試。

```python
@pytest.mark.skipif(sys.platform == "win32", reason="不支持 Windows 平台")
def test_platform():
    assert platform.system() == "Linux"
```
### 3. 預期失敗測試（XFail - Expected Failures）
- **`@pytest.mark.xfail` 使用**：標記預期失敗的測試，但測試套件不會因為這些失敗而報錯。
  
```python
@pytest.mark.xfail
def test_example_xfail():
    assert 1 == 2
```

- **帶條件的 `xfail`**：在特定條件下將測試標記為 `xfail`。

```python
@pytest.mark.xfail(condition=sys.version_info < (3, 7), reason="需要 Python 3.7 或更新版本")
def test_python_version():
    assert 1 == 1
```
### 4. 自定義標記測試（Custom Markers）
- **定義自訂標記**：在 `pytest.ini` 或 `conftest.py` 中添加自定義標記，並使用它們來組織測試。

```ini
[pytest]
markers =
    slow: 測試需要較長時間
    database: 與數據庫相關的測試
```

- **使用自定義標記**：展示如何對測試使用自定義標記。
  
```python
@pytest.mark.slow
def test_slow_operation():
    time.sleep(5)
    assert True
```
### 5. 執行帶標記的測試
- **選擇性運行標記測試**：使用 `-m` 參數運行特定標記的測試。
  
```sh
pytest -m slow
```

- **排除特定標記**：跳過特定標記的測試。
  
```sh
pytest -m "not slow"
```
