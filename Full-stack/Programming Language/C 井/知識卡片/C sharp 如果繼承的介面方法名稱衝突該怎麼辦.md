---
Date: 2024-02-23
tags:
---
如果一個類別實作了多個接口，而這些接口中有相同名稱的方法，就會導致方法名稱衝突。 為了解決這個問題，你可以使用明確介面實作或使用new關鍵字來隱藏基底類別中的方法。 以下是兩種方法的範例：

## 顯示接口實現
```C#
using System;

interface IInterface1
{
    void MyMethod();
}

interface IInterface2
{
    void MyMethod();
}

class MyClass : IInterface1, IInterface2
{
    void IInterface1.MyMethod()
    {
        Console.WriteLine("Implementation of MyMethod from IInterface1");
    }

    void IInterface2.MyMethod()
    {
        Console.WriteLine("Implementation of MyMethod from IInterface2");
    }
}

class Program
{
    static void Main()
    {
        MyClass myObject = new MyClass();

        ((IInterface1)myObject).MyMethod(); // 调用 IInterface1 中的方法
        ((IInterface2)myObject).MyMethod(); // 调用 IInterface2 中的方法
    }
}

```
## 使用 `new` 关键字隐藏基类中的方法：
```C#
using System;

interface IInterface1
{
    void MyMethod();
}

interface IInterface2
{
    void MyMethod();
}

class MyBaseClass : IInterface1
{
    public void MyMethod()
    {
        Console.WriteLine("Implementation of MyMethod from IInterface1");
    }
}

class MyDerivedClass : MyBaseClass, IInterface2
{
    // 使用 new 关键字隐藏基类中的方法
    new public void MyMethod()
    {
        Console.WriteLine("Implementation of MyMethod from IInterface2");
    }
}

class Program
{
    static void Main()
    {
        MyDerivedClass myObject = new MyDerivedClass();

        ((IInterface1)myObject).MyMethod(); // 调用 IInterface1 中的方法
        ((IInterface2)myObject).MyMethod(); // 调用 IInterface2 中的方法
    }
}

```