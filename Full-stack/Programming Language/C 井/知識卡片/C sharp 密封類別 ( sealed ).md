---
Date: 2024-02-23
tags:
---
## Sealed 關鍵詞
當對一個類應用sealed修飾符時，此修飾符會阻止其他類從該類繼承。

下面示例中，類 B 可以繼承類 A，**但是任何類不能繼承 B**
```C#
class A {}
    
sealed class B : A {}
```

## sealed 修飾方法或屬性
可以允許類別從基底類別繼承，並防止它們重寫特定的虛擬方法或虛屬性。
1. sealed是對虛方法或虛屬性，也就是同override一起使用，如果不是虛方法或虛屬性會報出錯誤：cannot be sealed because it is not an override
```C#
    public class A
    {
        protected  virtual  void M()
        {
            Console.WriteLine("A.M()");
        }
        protected  virtual  void N()
        {
            Console.WriteLine("A.N()");
        }
    }
    public class B:A 
    {
        protected override void M()
        {
            Console.WriteLine("B.M()");
        }
        protected sealed override void N()
        {
            Console.WriteLine("B.N()");
        }
    }
    public sealed class C:B
    {
        protected override void M()
        {
            Console.WriteLine("C.M()");
        }
        protected override void N()  //会报错 ："C.N():"继承成员"B.N()"是密封的，无法进行重写
        {
            Console.WriteLine("C.N()"); 
        }
    }
```