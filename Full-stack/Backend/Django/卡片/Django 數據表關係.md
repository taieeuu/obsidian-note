---
Date: 2024-08-17
---
在 Django 中，數據表關係通過模型之間的關聯來表示，這些關聯是基於關聯數據庫的概念實現的。Django 提供了三種主要的數據表關係來描述模型之間的關聯性：一對一（One-to-One）、一對多（Many-to-One）、多對多（Many-to-Many）。這些關係可以用來構建複雜的數據結構，並且通過 Django ORM（Object-Relational Mapping）來進行操作。
### 一對一關係（One-to-One Relationship）
一對一關係意味著一個模型的實例與另一個模型的實例有且只有一個對應關係。這種關係通常用來將兩個表格合併為一個更復雜的實體，或者將模型分解成更小的組件來減少表格的冗餘。
#### 在 Django 中實現一對一關係
使用 `OneToOneField` 來實現一對一關係。例如，假設我們有一個 `User` 模型和一個 `Profile` 模型，並且希望每個 `User` 都有唯一對應的 `Profile`：

```python
from django.db import models
from django.contrib.auth.models import User

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField()
    website = models.URLField()

    def __str__(self):
        return self.user.username
```

**`on_delete=models.CASCADE`**：這個選項確保當 `User` 被刪除時，對應的 `Profile` 也會被刪除。
### 一對多關係（Many-to-One Relationship）
一對多關係是最常見的數據表關係。一個模型的實例可以關聯到另一個模型的多個實例，而另一個模型的每個實例只能關聯到第一個模型中的一個實例。這種關係通常用於描述父子關係或層級結構。
##### 在 Django 中實現一對多關係
使用 `ForeignKey` 來實現一對多關係。例如，假設我們有一個 `Author` 模型和一個 `Book` 模型，每個 `Author` 可以寫多本 `Book`，但每本 `Book` 只有一個 `Author`：

```python
class Author(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)

    def __str__(self):
        return self.title
```
### 多對多關係（Many-to-Many Relationship）
多對多關係表示模型的多個實例可以與另一個模型的多個實例關聯。這種關係適合描述兩個模型之間的多重關聯，例如學生和課程，學生可以選擇多門課程，而每門課程也可以有多個學生選修。
#### 在 Django 中實現多對多關係
使用 `ManyToManyField` 來實現多對多關係。例如，假設我們有一個 `Student` 模型和一個 `Course` 模型，學生可以選擇多門課程，課程也可以有多個學生：

```python
class Student(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name

class Course(models.Model):
    title = models.CharField(max_length=200)
    students = models.ManyToManyField(Student)

    def __str__(self):
        return self.title
```

**`ManyToManyField`**：用於建立多對多關係，Django 自動創建一個中間表來處理兩個模型之間的多對多關係。
### Django 中的關聯查詢
Django ORM 提供了強大的查詢功能，可以輕鬆地操作這些關係。以下是一些常見的操作：
##### 一對一查詢
###### 獲取 `Profile` 的 `User`
```python
profile = Profile.objects.get(id=1)
user = profile.user
```
###### 獲取 `User` 的 `Profile`：
```python
user = User.objects.get(id=1)
profile = user.profile
```
##### 一對多查詢
###### 獲取 `Author` 的所有 `Book`：
```python
author = Author.objects.get(id=1)
books = author.book_set.all()
```
##### 多對多查詢
###### 獲取學生選修的所有課程：
```python
student = Student.objects.get(id=1)
courses = student.course_set.all()
```
###### 獲取課程的所有學生
```python
course = Course.objects.get(id=1)
students = course.students.all()
```