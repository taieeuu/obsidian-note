---
Date: 2024-02-07
tags:
  - Unity
---
>[!SUMMARY] Table of Contents

>        - [[區分 abstract、virtual、override 和 new#常用中文名稱|常用中文名稱]]
>        - [[區分 abstract、virtual、override 和 new#適用場合|適用場合]]
>        - [[區分 abstract、virtual、override 和 new#具體概念|具體概念]]
>        - [[區分 abstract、virtual、override 和 new#abstract 和 virtual 的區別：|abstract 和 virtual 的區別：]]
>    - [[區分 abstract、virtual、override 和 new#實例|實例]]
>        - [[區分 abstract、virtual、override 和 new#Part 1. Abstract|Part 1. Abstract]]
>            - [[區分 abstract、virtual、override 和 new#特點|特點]]
>            - [[區分 abstract、virtual、override 和 new#聲明|聲明]]
>            - [[區分 abstract、virtual、override 和 new#衍生類別的定義|衍生類別的定義]]
>            - [[區分 abstract、virtual、override 和 new#使用|使用]]
>            - [[區分 abstract、virtual、override 和 new#輸出|輸出]]
>        - [[區分 abstract、virtual、override 和 new#Virtual|Virtual]]
>            - [[區分 abstract、virtual、override 和 new#特點|特點]]
>            - [[區分 abstract、virtual、override 和 new#聲明與定義|聲明與定義]]
>            - [[區分 abstract、virtual、override 和 new#使用|使用]]
>            - [[區分 abstract、virtual、override 和 new#輸出|輸出]]
### 常用中文名稱
- abstract => 抽象方法。

-  virtual => 虛擬方法。

-  override => 覆蓋基礎類別方法。

-  new => 隱藏基礎類別方法。

- override 和 new 有時都叫覆寫基礎類別方法。

### 適用場合
- abstract 和 virtual 用在基礎類別(父類別)中

- override 和 new 用在派(衍)生類別（子類別）中。
### 具體概念
 - abstract 抽象方法，是空的方法，沒有方法實體，派(衍)生類必須以 override 實現此方法。

-  virtual 虛擬方法，若希望或預料到基礎類別的這個方法在將來的派(衍)生類別中會被覆寫（override 或 new），則此方法必須被聲明為 virtual。

- override 覆寫繼承自基礎類別的virtural方法，可以理解為拆掉老房子，在原址上建新房子，老房子再也找不到了（基礎類別方法永遠調用不到了）。

-  new 隱藏繼承自基礎類別的virtual方法，老房子還留着，在旁邊蓋個新房子，想住新房子的住新房子（作為衍生類別對象調用），想住老房子住老房子（作為基礎類別對象調用）。

- 當派(衍)生類別中出現與基礎類別同名的方法，而此方法前面未加 override 或 new 修飾符時，編譯器會報警告，但不報錯，真正執行時等同於加了new。
### abstract 和 virtual 的區別：
- abstract 方法還沒實現，連累着基礎類別也不能被實例化，除了作為一種規則或符號外沒啥用；virtual 則比較好，派(衍)生類別想覆寫就覆寫，不想覆寫就吃老子的。

- 而且繼承再好也是少用為妙，繼承層次越少越好，派(衍)生類別新擴展的功能越少越好，virtual 深合此意。
## 實例
### Part 1. Abstract
#### 特點
Abstract表示抽象類別，
利用Abstract修飾的內容代表方法的內容未完全實現
Abstract可以修飾 類，方法，屬性索引
標記為抽像或包含在抽象類別中的成員必須透過從抽象類別派生的類別來實現。
**特點**：
1. 不能被實例化 即不能使用 new方法
2. 抽象類別可以包含抽象方法和抽象存取器
3. 在方法或屬性中聲明
4. 抽象方法是隱式的虛方法
#### 聲明
```C#
public abstract class FunA  //抽象类
{
    public int NumberA = 100;
    public int NumberB = 200;

    public abstract void Swap();    //抽象方法

    public abstract int getNumberA { get; } //抽象方法
    public abstract int getNumberB { get; } //抽象方法

}
```
#### 衍生類別的定義
標記為抽像或包含在抽象類別中的成員必須透過從抽象類別派生的類別來實現
派生類別要實作基底類別用Abstract修飾的方法
```C#
public class FunB: FunA
{
    public override void Swap() //实现基类的SWAP方法
    {
        int temp = NumberA;
        NumberA = NumberB;
        NumberB = temp;
    }

    public override int getNumberA  //实现基类的getNumberA方法
    {
        get { return NumberA; }
    }

    public override int getNumberB  //实现基类的getNumberB方法
    {
        get { return NumberB; }
    }
}
```
#### 使用
```C#
    FunB funB = new FunB();
    Console.WriteLine("SWAP前 NumberA == {0}   NumberB == {1}", funB.NumberA, funB.NumberB);
    funB.Swap();
    Console.WriteLine("SWAP后 NumberA == {0}   NumberB == {1}", funB.getNumberA, funB.getNumberB);
```
#### 輸出
![[區分 abstract、virtual、override 和 new 1.png|300]]

### Virtual
#### 特點
*virtual關鍵字用於修飾方法、屬性、索引器或事件聲明，
* 並使它們可以在衍生類別中被重寫。 虛擬成員的實作可由衍生類別中的重寫成員來變更。
* 呼叫虛擬方法時，將為重寫成員檢查該物件的執行時間類型。 將呼叫大部分衍生類別中的該重寫成員，
* 如果沒有衍生類別重寫該成員，則它可能是原始成員。 預設情況下，方法是非虛擬的。 不能重寫非虛方法。
* virtual 修飾符不能與 static、abstract, private 或 override 修飾符一起使用。
* 和抽象方法的相同點是都用了override關鍵字重寫方法，
* 不同點是抽象方法必須存在於抽象類別中，而子類別必須重寫，除非子類別也是抽象的。
#### 聲明與定義
```C#
        public class Dimensions
        {
            public const double PI = Math.PI;
            public double NumberA;
            public double NumberB;

            public Dimensions(double NumberA, double NumberB)
            {
                this.NumberA = NumberA;
                this.NumberB = NumberB;
            }

            public virtual double Area()
            {
                return NumberA * NumberB;
            }

        }


        public class Circle : Dimensions
        {

            public Circle(double r) : base(r, 0) { }

            public override double Area()
            {
                return PI * NumberA * NumberA;
            }
        }

        public class Sphere: Dimensions
        {
            public Sphere(double r) : base(r, 0) { }

            public override double Area()
            {
                return 4 * PI * NumberA * NumberA;
            }
        }

        public class Cylinder: Dimensions
        {
            public Cylinder(double r, double h) : base(r, h) { }
            public override double Area()
            {
                return 2 * PI * NumberA * NumberA + 2 * PI * NumberA * NumberB;
            }
        }

```

#### 使用
```C#
            Dimensions P = new Circle(5.0);
            Console.WriteLine("Dimensions Area :" + P.Area());

            Sphere sphere = new Sphere(10);
            Console.WriteLine("Sphere Area :" + sphere.Area());

            Cylinder cylinder = new Cylinder(10, 10);
            Console.WriteLine("Cylinder Area :" + cylinder.Area());
```
#### 輸出
![[區分 abstract、virtual、override 和 new 2.png|348]]