---
Date:
---
### 序列化的基本概念
在 Django Rest Framework (DRF) 中，序列化（Serialization）是將複雜的數據類型（如 Django 的模型實例、QuerySet 等）轉換為 JSON、XML 或其他格式數據的過程。序列化器（Serializers）還負責將這些格式化的數據反序列化為複雜類型，以便在應用中使用。
#### 序列化的作用
- **數據轉換**：將 Django 模型實例或 QuerySet 轉換為 JSON、XML 等格式，便於通過 API 傳輸。
- **數據驗證**：在反序列化過程中驗證輸入數據的合法性，確保數據符合業務邏輯。
- **數據保存**：反序列化後的數據可以用來創建或更新模型實例，從而將數據保存到數據庫中。
### Django Rest Framework 中的序列化器類型
DRF 提供了多種序列化器類型，每種序列化器適用於不同的場景和需求。
#### Serializer 類
`Serializer` 類是 DRF 中的基礎序列化器，它類似於 Django 的表單類，允許你手動定義序列化器的每個字段以及它們的行為。
###### 基本使用示例
```python
from rest_framework import serializers

class ArticleSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(max_length=100)
    content = serializers.CharField()
    author = serializers.CharField(max_length=100)
    created_at = serializers.DateTimeField()

    def create(self, validated_data):
        return Article.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.title = validated_data.get('title', instance.title)
        instance.content = validated_data.get('content', instance.content)
        instance.author = validated_data.get('author', instance.author)
        instance.save()
        return instance
```
#### ModelSerializer 類
`ModelSerializer` 是 `Serializer` 的子類，專門用於與 Django 模型進行交互。它自動生成與模型對應的字段，並且提供了默認的 `create` 和 `update` 方法，大大簡化了序列化器的編寫。
###### 基本使用示例
```python
from rest_framework import serializers
from .models import Article

class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['id', 'title', 'content', 'author', 'created_at']
```

使用 `ModelSerializer` 時，DRF 會自動處理模型的字段映射，這使得代碼更簡潔，且便於維護。
#### HyperlinkedModelSerializer
`HyperlinkedModelSerializer` 是 `ModelSerializer` 的擴展，除了字段映射外，它還為關聯對象生成超鏈接，而不是僅僅使用主鍵 ID。這在 RESTful API 中非常有用，因為它能提供更豐富的 API 導航體驗。
###### 基本使用示例
```python
from rest_framework import serializers
from .models import Article

class ArticleSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Article
        fields = ['url', 'id', 'title', 'content', 'author', 'created_at']
        extra_kwargs = {
            'url': {'view_name': 'article-detail', 'lookup_field': 'pk'}
        }
```

在這個例子中，`url` 字段會自動生成一個指向該資源的超鏈接。
### 序列化器的常見功能
#### 3.1 字段驗證
序列化器在處理數據時自帶驗證功能，這些驗證可以是內置的，也可以是自定義的。
##### 基本字段驗證
DRF 提供了多種內置的字段驗證器，如 `MaxLengthValidator`、`MinValueValidator` 等。這些驗證器可以直接在定義字段時使用。

```python
class ArticleSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=100, min_length=5)
    content = serializers.CharField()
```
##### 自定義字段驗證
你也可以通過覆蓋 `validate_<field_name>` 方法來自定義單個字段的驗證邏輯。

```python
class ArticleSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=100)

    def validate_title(self, value):
        if 'django' not in value.lower():
            raise serializers.ValidationError("Title must contain the word 'django'.")
        return value
```
##### 全局驗證
如果需要基於多個字段的值進行驗證，你可以覆蓋序列化器的 `validate` 方法。

```python
class ArticleSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=100)
    content = serializers.CharField()

    def validate(self, data):
        if len(data['content']) < len(data['title']):
            raise serializers.ValidationError("Content must be longer than title.")
        return data
```
#### 自定義字段
有時候，你可能需要在序列化器中添加一些模型中沒有的自定義字段。這可以通過 `SerializerMethodField` 或 `CharField` 等來實現。
###### 使用 `SerializerMethodField`
`SerializerMethodField` 允許你在序列化器中添加一個計算字段或自定義字段。

```python
class ArticleSerializer(serializers.ModelSerializer):
    summary = serializers.SerializerMethodField()

    class Meta:
        model = Article
        fields = ['id', 'title', 'content', 'summary']

    def get_summary(self, obj):
        return obj.content[:50]  # 返回文章的前50個字符作為摘要
```
#### 3.3 序列化器的嵌套使用
DRF 支持在一個序列化器中嵌套使用其他序列化器，這對於處理關聯對象非常有用。
###### 示例：嵌套序列化器
假設 `Article` 有一個外鍵 `author` 指向 `User` 模型，你可以在 `ArticleSerializer` 中嵌套 `UserSerializer`。

```python
class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email']

class ArticleSerializer(serializers.ModelSerializer):
    author = UserSerializer(read_only=True)  # 嵌套 UserSerializer

    class Meta:
        model = Article
        fields = ['id', 'title', 'content', 'author']
```
#### 序列化器的局部更新
有時候你可能只想更新對象的某些字段，這在 PATCH 請求中很常見。DRF 支持在序列化器中進行局部更新。
###### 示例：局部更新
```python
class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['title', 'content']
        extra_kwargs = {'title': {'required': False}, 'content': {'required': False}}
```

這樣，你可以在進行局部更新時，只提供需要更新的字段即可。
#### 使用 `save()` 方法保存數據
序列化器的 `save()` 方法不僅僅是將數據保存到數據庫，它還可以被覆蓋來實現複雜的保存邏輯。
###### 示例：自定義 `save()` 方法
```python
class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['title', 'content', 'author']

    def save(self, **kwargs):
        article = Article(
            title=self.validated_data['title'],
            content=self.validated_data['content'],
            author=self.validated_data.get('author', None)
        )
        article.save()
        return article
```
### 結論
通過序列化器，你可以將 Django 模型和其他數據結構轉換為 JSON 或 XML 等格式，並進行數據驗證和處理。無論是使用基礎的 `Serializer`，還是使用便捷的 `ModelSerializer`，DRF 都提供了靈活的選項來滿足不同的需求。通過自定義序列化器，還可以實現更複雜的數據處理邏輯。
#### 使用場景
1. **構建 RESTful API**
    - DRF 序列化器是構建 RESTful API 的核心工具，用於將 Django 模型或其他 Python 數據結構轉換為 JSON、XML 等格式。這在構建需要與前端或其他服務進行數據交換的應用程序時特別有用。
2. **數據驗證和清理**
    - 序列化器不僅僅是將數據轉換為其他格式，還負責驗證和清理數據。例如，在創建或更新資源時，序列化器可以驗證輸入數據的格式和範圍，確保數據的有效性。
3. **處理複雜數據結構**
    - 當你需要處理具有多層嵌套結構的數據時，DRF 的序列化器可以通過嵌套其他序列化器來簡化處理。這對於有複雜關聯關係的 Django 模型特別有用，例如具有外鍵或多對多關係的模型。
4. **自定義數據處理邏輯**
    - 有時候，你需要在數據序列化或反序列化的過程中進行自定義邏輯處理，例如根據條件動態添加字段、計算某些字段的值等。DRF 序列化器提供了靈活的 API 來實現這些需求。
5. **API 安全性**
    - 通過序列化器，你可以控制哪些字段應該暴露給客戶端，哪些字段應該保密。這在處理涉及敏感信息的 API 時尤為重要。
#### 優點
1. **簡化 API 開發**
    - DRF 的序列化器大大簡化了 API 的開發過程。`ModelSerializer` 可以自動生成序列化邏輯，讓你專注於業務邏輯，而不需要手動處理數據的序列化和反序列化。
2. **內置驗證**
    - 序列化器自帶數據驗證功能，包括字段級別的驗證和全局驗證。這確保了只有有效的數據才能進入數據庫，大大減少了錯誤數據的風險。
3. **靈活的自定義能力**
    - DRF 序列化器允許開發者根據需求進行高度自定義。無論是定義自定義字段、重寫 `create` 和 `update` 方法，還是實現自定義的驗證邏輯，序列化器都提供了足夠的靈活性。
4. **良好的擴展性**
    - 當應用需求變得更複雜時，DRF 的序列化器可以通過繼承和重寫現有方法來實現複雜的數據處理邏輯，從而滿足多變的業務需求。
5. **與 Django 模型的無縫集成**
    - `ModelSerializer` 與 Django 的 ORM 緊密集成，可以自動映射模型字段，並處理模型實例的創建和更新。這使得在使用 Django ORM 開發的項目中，序列化器的使用非常高效和直觀。
6. **提高代碼可讀性和可維護性**
    - 使用序列化器來組織數據處理邏輯，使得代碼更加結構化和可讀。這不僅提高了代碼的可維護性，還減少了重複代碼，促進了代碼的可復用性。