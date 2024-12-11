---
Date: 2024-02-23
tags:
  - C_sharp
---
## 介紹
在C#中，委派（Delegate）是一種代表可以封裝方法的類型。委派可以用來定義、存儲和調用方法，使得在運行時可以動態地指定方法。它提供了一種方式，允許你在不知道具體方法的情況下引用方法，並在運行時動態替換這些方法。
##  定義委派：
在C#中，你可以使用 `delegate` 關鍵字來定義一個委派。以下是一個簡單的例子：
```C#
// 定義一個委派，代表一個可以接受兩個整數參數並返回一個整數的方法
public delegate int MathOperation(int a, int b);

```
## 實例化委派：

你可以實例化委派，並將它指向一個現有的方法。例如：
```C#
public class Calculator
{
    // 一個可以與 MathOperation 委派相匹配的方法
    public static int Add(int a, int b)
    {
        return a + b;
    }
}

// 在 Main 方法或其他地方實例化委派並指向特定方法
MathOperation operation = new MathOperation(Calculator.Add);

```
## 使用委派：

一旦你有了委派的實例，你可以像調用普通方法一樣調用它：
```C#
int result = operation(5, 3); // 調用 Calculator.Add 方法，返回 8

```
## 匿名方法和 Lambda 表達式：

除了使用命名方法外，你還可以使用匿名方法或Lambda表達式來創建委派的實例。這使得代碼更加簡潔和靈活。以下是使用Lambda表達式的例子：
```C#
MathOperation operation = (a, b) => a + b;
int result = operation(5, 3); // 返回 8
```

這裡，Lambda表達式 `(a, b) => a + b` 定義了一個可以替換 `Add` 方法的委派實例。

## 使用 Invoke 調用
  在C#中，`Invoke` 是一個用於調用委派實例所代表的方法的方法。委派是一種可以存儲對方法的引用的類型，而 `Invoke` 方法就是用來執行這個委派所引用的方法。
  
```C#
MathOperation operation = (a, b) => a + b;
int result = operation(5, 3); // 直接調用，返回 8
```

```C#
MathOperation operation = (a, b) => a + b; 
int result = operation.Invoke(5, 3); // 使用 Invoke 方法，返回 8
```

舉例:
```C#
public delegate int MathOperation(int a, int b);

public class Calculator
{
    public static int Add(int a, int b)
    {
        return a + b;
    }
}

class Program
{
    static void Main()
    {
        // 創建委派並指向 Calculator.Add 方法
        MathOperation operation = new MathOperation(Calculator.Add);

        // 使用 Invoke 方法調用委派
        int result1 = operation.Invoke(5, 3);
        Console.WriteLine("Result from Invoke: " + result1); // 輸出: 8

        // 委派可以被當作函數來調用，效果相同
        int result2 = operation(5, 3);
        Console.WriteLine("Result from direct invocation: " + result2); // 輸出: 8
    }
}

```