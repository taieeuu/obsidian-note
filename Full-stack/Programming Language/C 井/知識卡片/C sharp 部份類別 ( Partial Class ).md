---
Date: 2024-02-23
tags:
---
## partial 關鍵詞
部分類別使用時機大都在處理大型專案時，將類別分散至個別檔案，可讓多位程式設計師進行運作。

程式碼說明建立部分類別的方法，就是加上關鍵子『partial』。

```c#
	//部分類別(Employee其中一個組件)
    public partial class Employee
    {
        public string PracticeTime;
       
        public void DoWork()
        {
        }
    }

    //部分類別(Employee其中一個組件)
    public partial class Employee
    {
        public string   GoToDinner()
        {
            return "GoToDinner" ;
        }
    }

 	
 	protected void Page_Load(object sender, EventArgs e)
   	{
            Employee Employ = new Employee { PracticeTime = "100" 
            TextBox1.Text = Employ.GoToDinner();
            TextBox6.Text = Employ.PracticeTime;     
    }
```

執行結果:
```C#
GoToDinner
100
```
## 部分類別的特性

- 所有組件都必需**在相同的命名空間(Namespace)**
- 如果有任何組件宣告為`abstract`則被視為抽象，如果宣告為`Sealed`則被視為密封，如果其中一繼承某基底類別，整個型別都會繼承
- 任何組件都可以指定不同的基底介面，最後的型別會實作所有部份宣告的任何介面
## 部份類別方法的特性
部分方法的簽章是在**部分型別中的一部分定義，而其實作是在型別的另一部分中定義。**
```c#
//部份類別
 public partial class Employee
{
	public string PracticeTime;

	//定義方法
	partial void DoWork();
   

}

//部份類別
public   partial class Employee
{
 //部分方法，實作。
	partial void DoWork()
	{
	  Console .WriteLine("DoWork~~~" );
	}


	public   string   GoToDinner()
	{
	  return "GoToDinner" ;
	}
}
```

如果其中一個部分型別改成

```C#
partial void DoWork()
{
}
```

編譯器會打斷他的腿。

### 部份方法其它特性

- 部分型別**兩個部分中的簽章必須相符**
- **方法必預回傳`void`**
- **沒有存取修飾詞是被允許的**
- **如果沒有任何一個部分方法提供實作，則編譯器便會移除該方法和所有的呼叫。**
- **部份方法都是隱含`private`，因此不能`virtual`**