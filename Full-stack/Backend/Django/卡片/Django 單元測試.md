---
Date: 2024-08-21
---
單元測試是指針對應用程式中的最小可測單位（如函數、方法）進行自動化測試，以確保其行為符合預期。在 Django 中，單元測試是非常重要的開發過程，可以幫助你驗證各個功能的正確性，並在未來進行代碼修改時防止出現回歸錯誤。

Django 提供了一個內建的測試框架，基於 Python 的 `unittest` 模組進行擴展，讓開發者可以輕鬆地編寫和運行測試。
### 定義測試類
Django 的單元測試在項目應用的 `test`。你也可以根據需要在應用中創建一個 `tests` 目錄，並將不同的測試拆分成多個文件。

```sh
myapp/
    __init__.py
    models.py
    views.py
    ...
    tests.py  # 單個測試文件
    # 或者
    tests/
        __init__.py
        test_models.py
        test_views.py
        test_forms.py
        ...
```
#### 編寫基本測試用例
```python
from django.test import TestCase
from django.urls import reverse
from myapp.models import MyModel

class MyModelTest(TestCase):
    def setUp(self):
        # 設置測試數據
        MyModel.objects.create(name="Test 1", value=100)
        MyModel.objects.create(name="Test 2", value=200)

    def test_model_creation(self):
        # 測試對象是否被正確創建
        obj1 = MyModel.objects.get(name="Test 1")
        obj2 = MyModel.objects.get(name="Test 2")
        self.assertEqual(obj1.value, 100)
        self.assertEqual(obj2.value, 200)

    def test_view_response(self):
        # 測試視圖是否返回正確的狀態碼
        response = self.client.get(reverse('my_view_name'))
        self.assertEqual(response.status_code, 200)
```
#### 測試模型
在單元測試運行的過程中，會創建一個虛擬數據庫，所以模型數據操作都在虛擬數據庫中完成。

```python
class MyModelTest(TestCase):
    def test_string_representation(self):
        obj = MyModel(name="Sample Name")
        self.assertEqual(str(obj), obj.name)

    def test_model_save(self):
        obj = MyModel(name="Test Save", value=500)
        obj.save()
        self.assertEqual(MyModel.objects.count(), 1)
        self.assertEqual(MyModel.objects.get(name="Test Save").value, 500)
```
#### 測試視圖
```python
from django.urls import reverse

class MyViewTest(TestCase):
    def test_view_url_exists_at_proper_location(self):
        response = self.client.get('/my-url/')
        self.assertEqual(response.status_code, 200)

    def test_view_url_accessible_by_name(self):
        response = self.client.get(reverse('my_view_name'))
        self.assertEqual(response.status_code, 200)

    def test_view_uses_correct_template(self):
        response = self.client.get(reverse('my_view_name'))
        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, 'myapp/template.html')
```
#### 測試表單
```python
from myapp.forms import MyForm

class MyFormTest(TestCase):
    def test_valid_form(self):
        data = {'name': 'Test Name', 'value': 123}
        form = MyForm(data=data)
        self.assertTrue(form.is_valid())

    def test_invalid_form(self):
        data = {'name': '', 'value': 123}  # 缺少 name
        form = MyForm(data=data)
        self.assertFalse(form.is_valid())
```
### 運行測試
```python
python manage.py test
```

Django 會自動檢測 `tests.py` 文件或 `tests` 目錄中的測試用例並執行它們。測試結果將顯示在終端中，並且你可以根據需要調整測試代碼或應用代碼。
### 使用 Fixtures
Fixtures 是預定義的數據集，可以在測試期間用於初始化數據庫。你可以將 Fixtures 定義為 JSON、XML 或 YAML 格式，然後在測試中加載它們。

```sh
myapp/
    fixtures/
        initial_data.json
```

在測試用例中使用 Fixtures：

```python
class MyModelTest(TestCase):
    fixtures = ['initial_data.json']

    def test_fixture_loading(self):
        obj = MyModel.objects.get(pk=1)
        self.assertEqual(obj.name, "Fixture Name")
```
### 模擬和補丁（Mocking and Patching）
有時候，你可能需要在測試中模擬一些行為，例如外部 API 調用。可以使用 Python 的 `unittest.mock` 模組來實現這些模擬。

```python
from unittest.mock import patch

class MyViewTest(TestCase):
    @patch('myapp.views.external_api_call')
    def test_mock_external_api_call(self, mock_api_call):
        mock_api_call.return_value = {'status': 'success'}
        response = self.client.get(reverse('my_view_name'))
        self.assertEqual(response.context['api_result'], {'status': 'success'})
```
### 測試覆蓋率
可以使用 `coverage.py` 來測量測試的覆蓋率，確保你沒有漏測的代碼部分。

```sh
pip install coverage
```

運行測試並生成覆蓋率報告：

```bash
coverage run manage.py test
coverage report
```