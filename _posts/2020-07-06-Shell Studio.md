---
# layout: page
title: C# unity 日记本
categories: [强调, 代码语言]
tags: [Shell Studio, C#, Unity]
# excerpt: C# UNITY edit record for Shell Studio
date: 2020-07-06
comments: false
excerpt_separator:  '<!-- more -->'
# project: true
---

C# 学习历程记录.  
<!-- more -->

# 日常

## 2020-07-06

*类型*  
```c#
public enum e // 枚举类型
{
    spring = 0,
    summer = 100,
    autumn = 51
}
class Program
{
    static void Main(string[] args)
    {
        int i = 1;
        float f = 1.01F;
        double d = 1.125454;
        bool b = false; //or true
        char c = 'a';
        string s = "string";

        var tuple = ("spring", 1);
        int[] a1 = new int[3];
        a1[0] = 1;
        // a1[2] = new int[3];
        int[] a2 = new int[] { 1, 2, 3 };


        Console.WriteLine(e.spring);
        Console.WriteLine((int)e.spring);
        Console.WriteLine(tuple);
    }
}
```

```
spring  
0  
(spring, 1)  
```

*语句*  
* `if` `switch` `while` `for` `do` `break` `continue` `return` `throw` `try`皆不做详细解释.  
* `foreach`  
```c#
string[] s = {"a","b"};
foreach(string str in s){
    Console.WriteLine(str);
}
```

* `goto`  
```c#
Console.WriteLine("out one");
goto check;
Console.WriteLine("out two");
check:
    Console.WriteLine("out three");
```

```
out one  
out three  
```

* `yield`  
迭代器:可在容器（container，例如链表或阵列）上遍访的接口，设计人员无需关心容器的内容。
```c#
static System.Collections.Generic.IEnumerable<string> out_num()
{
    yield return "start";
    for (int i = 1; i < 3; i++)
    {
        yield return i.ToString();
    }
    yield return "end";
}
static void Main(string[] args)
{
    foreach (string item in out_num())
    {
        Console.WriteLine(item);
    }
}
```

```
start  
1  
2  
end  
```

* `checked`和`unchecked`  
如果使用了checked发生数学运算溢出时会抛出OverflowException；如果使用了unchecked则不会检查溢出，算错了也不会报错。
```c#
int x = int.MaxValue;
unchecked
{
    Console.WriteLine(x + 1);  // Overflow
}
checked
{
    Console.WriteLine(x + 1);  // Exception
}
```

* `lock`  
用于获取给定对象的相互排斥锁定，执行语句，然后解除锁定。

* `using`  
using 语句用于获取资源，执行语句，然后释放资源。
```c#
using (TextWriter w = File.CreateText("test.txt"))
{
    w.WriteLine("Line one");
    w.WriteLine("Line two");
    w.WriteLine("Line three");
}
```

## 2020-07-11
 
关键词:`namespace` `using` `base` `this` `static` `const` `readonly` `ref` `out` `get` `set` `virtual` `abstract` `override` `public` `protected` `private` `internal` `class`  

*可访问性*
* public 访问不受限制.
* protected 访问仅限于此类或派生自此类的类.
* private 访问仅限于此类.
* internal 访问仅限于当前程序集（.exe、.dll 等）.
* protected internal 访问仅限于包含类、派生自包含类的类或同一程序集中的类.
* private protected 访问仅限于同一程序集中的包含类或派生自包含类的类.


*泛型类*
```c#
public class Pair<TFirst,TSecond>
{
    public TFirst First;
    public TSecond Second;
}

Pair<int,string> pair = new Pair<int,string> { First = 1, Second = "two" };
```

*All Test*
```c#
namespace testclass
{
    public class point
    {
        public point(int x, int y)
        {
            Console.WriteLine("new point.");
            this.x = x;
            this.y = y;
            this.message = "point 声明.(在构造中初始化)";
        }
        public point()
        {

        }
        ~point()
        {
            Console.WriteLine("end ~point.");
        }
        private int x = 1;
        protected int y = 10;
        protected readonly string message = "point 声明.";
        public const string user_name = "original04";
        public int X
        {
            get
            {
                return x;
            }
            set
            {
                this.x = value;
            }
        }
        public virtual int Y
        {
            get;
        }
    }

    public class point3D : point
    {
        public point3D(int x = 0, int y = 0, int z = 0) : base(x, y)
        {

            this.z = z;
        }
        private int z = 1;
        public int Z
        {
            get
            {
                return z;
            }
        }
        public override int Y
        {
            get
            {
                return base.y;
            }
        }
        public string coordinate
        {
            get
            {
                base.X.ToString();
                return "(" + base.X.ToString() +
                    "," + Y.ToString() +
                    "," + Z.ToString() + ")";
            }
        }
    }

}

class Program
{
    public static void testdef(ref int x, ref int y, out int result, out int sum)
    {
        x = 1;
        y = 1;

        result = x * y;
        sum = x + y;
    }
    static void Main(string[] args)
    {
        testclass.point3D p1 = new testclass.point3D(10, 10, 10);
        Console.WriteLine($"p1.X:{p1.X},p1.Y:{p1.Y},p1.Z:{p1.Z},p1.coordinate:{p1.coordinate}.");
        Console.WriteLine("p1.X.set run...");
        p1.X = 1;
        Console.WriteLine($"p1.X:{p1.X},p1.Y:{p1.Y},p1.Z:{p1.Z},p1.coordinate:{p1.coordinate}.\n");
        int a = 0;
        int b = 0;
        int result = 0;
        int sum = 0;
        Console.WriteLine($"a:{a},b:{b},result:{result},sum:{sum}.");
        testdef(ref a,ref b,out result,out sum);
        Console.WriteLine("testdef run...");
        Console.WriteLine($"a:{a},b:{b},result:{result},sum:{sum}.");
    }
}
```

```
new point.  
p1.X:10,p1.Y:10,p1.Z:10,p1.coordinate:(10,10,10).  
p1.X.set run...  
p1.X:1,p1.Y:10,p1.Z:10,p1.coordinate:(1,10,10).  
a:0,b:0,result:0,sum:0.  
testdef run...  
a:1,b:1,result:1,sum:2.  
```

## 2020-07-15

关键词:`delegate` `event` `interface`
delegate与event的定义方式：
```c#
public delegate void makeDelegate(string name); // 委托
public event makeDelegate MDelegate; // 事件
```

*ALL TEST*  
*委托与事件*
```c#
namespace Delegate_test
{
    public delegate void makeDelegate(string name);
    public class MakePrint{
        public void English_Make(string name){
            Console.WriteLine("good morning" + name);
        }
        public void Chinese_Make(string name){
            Console.WriteLine("早上好" + name);
        }
    }


    public class people{
        public event makeDelegate MDelegate;

        public void  display(string name){
            MDelegate(name);
        }
    }

}

class Program
{

    static void ConsoleName(string name, Delegate_test.makeDelegate Delegate_function){
        Delegate_function(name);
    }
    static void Main(string[] args)
    {
        Console.WriteLine("---------test 1---------");
        Delegate_test.MakePrint mp = new Delegate_test.MakePrint();
        ConsoleName("程成",mp.Chinese_Make);
        ConsoleName("chen cheng",mp.English_Make);

        Console.WriteLine("---------test 2---------");
        Delegate_test.makeDelegate MDelegate;
        MDelegate = mp.Chinese_Make;
        MDelegate += mp.English_Make;
        ConsoleName("chen cheng",MDelegate);

        Console.WriteLine("---------test 3---------");
        Delegate_test.people P = new Delegate_test.people();
        P.MDelegate += mp.Chinese_Make;
        P.display("程成");
    }
}
```
*接口*  
[from docs.microsoft.com](https://docs.microsoft.com/zh-cn/dotnet/csharp/tour-of-csharp/interfaces)