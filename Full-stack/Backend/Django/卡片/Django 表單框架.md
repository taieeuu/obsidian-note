---
Date: 2024-08-25
---
### 類型
Django 表單主要有兩種類型：

- **Django Forms**：使用 `django.forms` 模塊來手動創建表單。這種方式允許你完全控制表單的每一個字段和驗證邏輯，非常靈活。
- **Model Forms**：基於 Django 模型（Model）來創建表單，這種方式可以自動生成與模型字段相對應的表單字段，並且內建數據驗證功能。
### 創建 Django 表單
#### 使用 django.forms 創建表單
要創建一個表單，首先需要定義一個繼承自 `forms.Form` 的類，並在其中定義表單的字段。例如：

```python
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField(label='Name', max_length=100)
    email = forms.EmailField(label='Email')
    message = forms.CharField(widget=forms.Textarea, label='Your Message')
```

我們創建了一個聯絡表單，包含 `name`、`email` 和 `message` 三個字段。每個字段都是 `forms` 模塊中的一個類型，並且可以接受各種參數來定義其行為。
#### 使用 ModelForm 創建表單
如果表單的字段直接對應於某個模型，可以使用 `ModelForm` 自動創建表單。這樣可以減少重複代碼，並確保表單和模型之間的一致性。例如：

```python
from django import forms
from .models import Article

class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = ['title', 'content', 'author']
```
### 使用 Django 表單
#### 3.1 在視圖中處理表單
要在視圖中處理表單，可以使用以下步驟：

1. **初始化表單**：通過 GET 請求，渲染空表單；通過 POST 請求，將提交的數據綁定到表單。
2. **驗證表單**：調用 `is_valid()` 方法檢查表單數據是否有效。
3. **保存數據**：如果表單數據有效，可以進行保存或進一步處理。
###### FBV 示例
```python
from django.shortcuts import render, redirect
from .forms import ContactForm

def contact_view(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            # 表單數據有效，可以進行處理
            # 比如發送電子郵件或保存到數據庫
            return redirect('success')
    else:
        form = ContactForm()
    return render(request, 'contact.html', {'form': form})
```
###### CBV示例
```python
from django.urls import reverse_lazy
from django.views.generic.edit import FormView
from .forms import ContactForm

class ContactView(FormView):
    template_name = 'contact.html'
    form_class = ContactForm
    success_url = reverse_lazy('success')

    def form_valid(self, form):
        # 在此處理表單數據
        # 比如發送電子郵件
        return super().form_valid(form)
```
#### 在模板中渲染表單
```python
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Submit</button>
</form>
```
#### 進階技巧
- **表單字段小部件（Widgets）**：可以通過設置字段的小部件來定制表單的輸入方式，例如使用 `forms.Textarea` 將文本字段變成多行文本框。
- **表單字段的初始化值**：可以在初始化表單時傳遞 `initial` 參數來設置字段的默認值。
- **非模型字段（Non-model fields）**：在 `ModelForm` 中，你可以添加一些非模型字段，以便用於模型以外的數據處理。
- **表單集（Formsets）**：Django 表單集允許你同時處理多個相同表單的集合，非常適合處理多行輸入或批量更新操作。