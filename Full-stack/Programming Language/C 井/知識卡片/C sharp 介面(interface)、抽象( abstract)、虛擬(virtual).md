---
Date: 2024-02-22
tags:
---
## 抽象類 ( Abstract class )
C# 身為一個 OOP語言，自然希望程式以_物件(Object)的方式進行開發，所以我們會先定義類別(Class)，類別可以視為是物件的藍圖，當類別被建立(New)時，就成為了一個物件

```C#
class Car
{
	public string model; //型號
	public int CC; //CC數
	public decimal price; //
	
	public string run() {
	return $"{model} 跑超快";
	}
}
```

實例:

```C#
class Program
{
	static void Main(string[] args)
	{
		Car CarA = new Car();
		CarA.model = "海神";
		Car CarB = new Car() { model = "小50" };
		Console.WriteLine(CarA.run());
		Console.WriteLine(CarB.run());
	}
}

# --result--
海神 跑超快
小50 跑超快
```

結果兩台都跑超快，這樣好像不太對…。雖然都是車子而且都會跑，但是速度應該是有差的。而且海神跟小50之間應該有更多的差異。所以顯然這樣的設計太籠統了。

於是我們可以將這些共同項目**訂為抽象(abstract)類別，由子類別繼承( extends)並將須自行定義的動作設為_抽象(abstract)方法，由子類別自行覆寫(override)。**


**抽象方法 `(abstract)` 只能定義不能實作且須為公開(public)**
```C#
using System;
using System.Collections.Generic;
using System.Text;

namespace ConsoleApp1
{
    //車子的共同屬性與行為
    abstract class Car
    {
        public string model; //型號
        public int CC;  //CC數
        public decimal price; //

        public string Start()
        {
            return $"引擎啟動";
        }

        abstract public string run();
       
    }

    //跑車
    class SportsCar : Car
    {
        //乘客數
        public int passengers = 4;
        //幾門
        public int Door = 4;
        public string opneDoor(){
            retrun "側開";
        }
        
        public override string run()
        {
            return $"{model} 跑超快";
        }
    }

    //機車
    class motorcycle : Car
    {
        //乘客數
        public int passengers = 2;
        public override string run()
        {
            return $"{model} 跑不快";
        }
    }

}
```

**抽象類別不能實作**
```C#
 class Program
    {       
        static void Main(string[] args)
        {
            SportsCar CarA = new SportsCar();
            CarA.model = "海神";
            motorcycle CarB = new motorcycle() { model = "小50" };
            Console.WriteLine(CarA.Start() + " " + CarA.run());
            Console.WriteLine(CarB.Start() + " " + CarB.run());
           
        }
    }
# --result--
引擎啟動 海神 跑超快
引擎啟動 小50 跑不快

```

跑車與機車都繼承了Car這個抽象類別並各自覆寫了run()這個方法之後，我們就可以更明確的區分跑車與機車之間的差異。

>**補充：**  
1.**抽象類別不能實作**。因為設計邏輯上屬於一個未完整的類別。  
2.**抽象類別中可以定義抽象方法但不能實作且必須為公開(public)**。因為這部份是要開放給子類別複寫的。  
3.**抽象類別可以繼承抽象類別**，但是一樣不能實作抽象方法  
4.**繼承抽象類別的子類必須複寫父類的的抽象方法**
## 虛擬 ( Virtual ) 
同時我們在跑車的類別上增加了openDoor()這個方法，並回傳為側開。但是少數車種開門方法是上掀式的，難道又要將跑車類別改成*抽象類別*，然後再各自實作嗎？這顯然有點因噎廢食，這時候可以使用*虛擬(virtual)方法*

**虛擬(virtual)方法可以實作並且允許繼承的子類別複寫。**
```C#
using System;
using System.Collections.Generic;
using System.Text;

namespace ConsoleApp1
{
    abstract class Car
    {
        public string model; //型號
        public int CC;  //CC數
        public decimal price; //

        public string Start()
        {
            return $"引擎啟動";
        }

        abstract public string run();
       
    }

    //跑車
    class SportsCar : Car
    {
        //乘客數
        public int passengers = 4;
        //幾門
        public int Door = 4;

        public virtual string openDoor() {
            return "側開";
        }

        public override string run()
        {
            return $"{model} 跑超快";
        }
    }

    //上開車款
    class SpecialSportsCar : SportsCar
    {
        public override string openDoor()
        {
            return "上開";
        }

    }

    //機車
    class motorcycle : Car
    {
        //乘客數
        public int passengers = 2;
        public override string run()
        {
            return $"{model} 跑不快";
        }
    }

}
```

```C#
using System;

namespace ConsoleApp1
{
    class Program
    {       
        static void Main(string[] args)
        {
            SportsCar CarA = new SportsCar();
            CarA.model = "海神";
            motorcycle CarB = new motorcycle() { model = "小50" };
            SpecialSportsCar CarC = new SpecialSportsCar() { model = "蝙蝠車" };
            Console.WriteLine(CarA.Start() + " " + CarA.run() + " " + "開門方式" + CarA.openDoor());
            Console.WriteLine(CarB.Start() + " " + CarB.run() + " ");
            Console.WriteLine(CarC.Start() + " " + CarC.run() + " " + "開門方式" + CarC.openDoor());

        }
    }
}
#  --result--
引擎啟動 海神 跑超快 開門方式側開
引擎啟動 小50 跑不快
引擎啟動 蝙蝠車 跑超快 開門方式上開

```

>**補充：**  
1.**虛擬方法必須實作**。  
2.**虛擬方法中必須為公開(public)**。因為允許子類別複寫。  
3.**子類別可以直接引用或選擇複寫(override)虛擬方法**

## 面 ( interface )
現在我們有了三種車款可以開始賣車了。但是有買賣就必須繳稅，所以我們新增一個計算稅率的方法。

```C#
using System;
using System.Collections.Generic;
using System.Text;

namespace ConsoleApp1
{
    abstract class Car
    {
        public string model; //型號
        public int CC;  //CC數
        public decimal price; //售價
        public decimal TaxRate; //稅率
        public string Start()
        {
            return $"引擎啟動";
        }

        abstract public string run();

        //要繳給國家的稅
        public decimal Tax() {
            return price * TaxRate;
        }
    }

    //跑車
    class SportsCar : Car
    {
        //乘客數
        public int passengers = 4;
        //幾門
        public int Door = 4;

        public virtual string openDoor() {
            return "側開";
        }

        public override string run()
        {
            return $"{model} 跑超快";
        }
    }

    //上開車款
    class SpecialSportsCar : SportsCar
    {
        public override string openDoor()
        {
            return "上開";
        }

    }

    //機車
    class motorcycle : Car
    {
        //乘客數
        public int passengers = 2;
        public override string run()
        {
            return $"{model} 跑不快";
        }
    }

}
```

```C#
using System;

namespace ConsoleApp1
{
    class Program
    {       
        static void Main(string[] args)
        {
            SportsCar CarA = new SportsCar();
            CarA.model = "海神";
            CarA.price = 5000000000;
            CarA.TaxRate = 0.3M;
            motorcycle CarB = new motorcycle() { model = "小50" ,price=30000 , TaxRate=0.1M};
            SpecialSportsCar CarC = new SpecialSportsCar() { model = "蝙蝠車", price = 3000000000, TaxRate = 0.4M };
            Console.WriteLine($"{CarA.model} 售價：{CarA.price} 需繳稅：{CarA.Tax().ToString("N0")}");
            Console.WriteLine($"{CarB.model} 售價：{CarB.price} 需繳稅：{CarB.Tax().ToString("N0")}");
            Console.WriteLine($"{CarC.model} 售價：{CarC.price} 需繳稅：{CarC.Tax().ToString("N0")}");


        }
    }
}

# --result--
海神 售價:5000000000 需繳稅:1500000000
小50 售價:30000 需繳稅:3000
蝙蝠車 售價:3000000000 需繳稅:1200000000
```

看似這樣就完成了。但是不只有賣車要繳稅，只要是涉及買賣都須要繳稅，將繳稅這個動作放在_Car_這個類別顯然是不合適的。

定義這種橫跨類別的動作及屬性便是*介面(Interface)。*

我們簡單定義一個 _IProduct_的介面，裡面有 _model、price_跟_Tax_ 這兩個屬性跟方法。然後由 _Shoes_與 _Car_ 這兩個類別去實作這個介面。

```C#
using System;
using System.Collections.Generic;
using System.Text;

namespace ConsoleApp1
{
    interface IProduct {
        //string model; --錯誤的寫法。介面不能實作，只能宣告
        //型號
        string model { get; set; }//宣告 model這個屬性需有 get 與 set 兩個方法
        decimal price { get; set; }
        //要繳給國家的稅
        decimal Tax();      
    }

    //鞋子
    class Shoes : IProduct
    {
        //string model; --錯誤的寫法。必須明確實作介面屬性的方法
        /*屬性宣告的完整寫法
        private string _model = "";
        string IProduct.model {
            get {
                return _model;
            }
            set {
                _model = value;
            }
        }
        */
        public string model { get; set; } //語法糖。與介面的 string model { get; set; } 是不一樣的意思 
        public decimal price { get; set; } //售價
        public decimal TaxRate; //稅率
        public int Size;
        public decimal Tax()
        {
            return price * TaxRate;
        }

    }

    abstract class Car: IProduct
    {
        public string model { get; set; } //型號
        public int CC;  //CC數
        public decimal price { get; set; } //售價
        public decimal TaxRate; //稅率

     
        public string Start()
        {
            return $"引擎啟動";
        }

        abstract public string run();
       
        public decimal Tax() {
            return price * TaxRate;
        }

    }

    //跑車
    class SportsCar : Car
    {
        //乘客數
        public int passengers = 4;
        //幾門
        public int Door = 4;

        public virtual string openDoor() {
            return "側開";
        }

        public override string run()
        {
            return $"{model} 跑超快";
        }
    }

    //上開車款
    class SpecialSportsCar : SportsCar
    {
        public override string openDoor()
        {
            return "上開";
        }

    }

    //機車
    class motorcycle : Car
    {
        //乘客數
        public int passengers = 2;
        public override string run()
        {
            return $"{model} 跑不快";
        }
    }

     
}
```

```C#
using System;

namespace ConsoleApp1
{
    class Program
    {       
        static void Main(string[] args)
        {
            SportsCar CarA = new SportsCar();
            CarA.model = "海神";
            CarA.price = 5000000000;
            CarA.TaxRate = 0.3M;
            motorcycle CarB = new motorcycle() { model = "小50" ,price=30000 , TaxRate=0.1M};
            Shoes ShoesA = new Shoes() { model = "愛迪達", price = 3000, TaxRate = 0.07M };
            Console.WriteLine($"{CarA.model} 售價：{CarA.price} 需繳稅：{CarA.Tax().ToString("N0")}");
            Console.WriteLine($"{CarB.model} 售價：{CarB.price} 需繳稅：{CarB.Tax().ToString("N0")}");
            Console.WriteLine($"{ShoesA.model} 售價：{ShoesA.price} 需繳稅：{ShoesA.Tax().ToString("N0")}");


        }
    }
}

# --result--
海神 售價:5000000000 需繳稅:1500000000
小50 售價:30000 需繳稅:3000
蝙蝠車 售價:3000000000 需繳稅:210
```

於是不論是甚麼商品只要實作了 *IProduct*的這個介面都會擁有繳稅這個行為真是太好了

但是好像有哪裡怪怪的。結果都是要各自實作，繼承了這個介面除了會在編譯的時候報錯之外好像沒甚麼用啊(編譯的時候會報錯就很有用啦~~)。

**介面最大的功用就是讓不同的類別之間產生關聯性**。我們可以確定只要有實作了 **IProduct_這個介面的類別都擁有 model與 Tax()這兩個屬性與方法。**

所以我們可以將程式改寫為

```C#
using System;
using System.Collections.Generic;

namespace ConsoleApp1
{
    class Program
    {       
        static void Main(string[] args)
        {
            SportsCar CarA = new SportsCar();
            CarA.model = "海神";
            CarA.price = 5000000000;
            CarA.TaxRate = 0.3M;
            motorcycle CarB = new motorcycle() { model = "小50" ,price=30000 , TaxRate=0.1M};
            Shoes ShoesA = new Shoes() { model = "愛迪達", price = 3000, TaxRate = 0.07M };

            List<IProduct> proList = new List<IProduct>();
            proList.Add(CarA);
            proList.Add(CarB);
            proList.Add(ShoesA);

            proList.ForEach(o =>
            {
                Console.WriteLine(pritTax(o));
            });

           
        }

        //印出繳稅結果
        static string pritTax(IProduct product) {
            return $"{product.model} 售價：{product.price} 需繳稅：{product.Tax().ToString("N0")}";
        }
    }
}
# --result--
海神 售價:5000000000 需繳稅:1500000000
小50 售價:30000 需繳稅:3000
蝙蝠車 售價:3000000000 需繳稅:210

```

>**補充：**  
1.**介面只能宣告，不能實作，且只能為公開(public)**。 預設就是公開所以不用特別加上public  
2.**介面可以繼承介面**
3.**介面不可以繼承類別**
4.**可以同時繼承多個介面**
5.**繼承介面的類別必須實作所有的屬性與方法**。