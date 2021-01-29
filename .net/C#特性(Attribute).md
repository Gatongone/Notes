# C#特性(Attribute)

   特性是一种能用于为元数据添加额外信息的类，可以用通过方括号([])的方式在元素（可以是类、接口、成员、函数、函数参数等）前声明标签。标签则代表该元素拥有该特性。之后可以通过反射的方式获取特性。
</br>
</br>
</br>

* ## 标签

    标签的本质其实是构造函数，写在元素的上一行表示他们具有该特性的信息,相当于声明了一个类对象关联到该元素。如果特性类名是XXXAttribute，那么在掉用标签时会自动省略Attribute，比如  [XXX("it is a string",5)] 。
</br>
</br>
</br>

* ## 常用的方法
    1. ### 判断是否使用了某特性
        ```C#
        //memberinfo:元素据
        //参数1：是哪一种特性的类型
        //参数2：是否查找继承链（父类有这个特性算不算）
        //返回一个布尔值
        memberinfo.IsDefined(typeof(attribute),false);
        ```
    </br>

    2. ### 获得特性
        ```C#
        //获得元素据的全部特性
        //参数：是否查找继承链
        MyCustomAttribute[] mca = memberinfo.GetCustomAtrributes(true);
        
        //获得元素据的某个特性
        //参数为指定特性类型
        //MyCustomAttribute 为自定义特性
        MyCustomAttribute mca = memberinfo.GetCustomAttribute(typeof(MyCustomAttribute)) as MyCustomAttribute;
        ```
</br>
</br>
</br>

* ## 常用预定义特性
    
    1. ### 限定特性类的使用范围(该标签只能写在特性类上)：
        ```C#
        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Interface,AllowMultiple = true, Inherited = true)]
        //参数1：该特性只能使用在哪些范围
        //参数2：是否允许元数据共有多个特性(默认都是false)
        //参数3：特性是否被派生类和重写函数继承
        //该语句的意思是该只能使用在class和interface上，允许多个特性同时存在，允许特性被子类继承
        ```
    </br>

    2. ### 成员过时警告
        ```C#
        [Obsolete("该类成员已经过时",true)]
        //参数1：警告信息
        //参数2：使用该类成员时： true时直接报错，false时会发出警告
        ``` 
    </br>

    3. ### 调用者信息
       需要引入命名空间：using System.Runtime.ComplierServices

        ```C#
        //调用该对象者所在文件路径
        [CallerFilePath]
        
        //调用该对象者所在行数
        [CallerLineNumber]

        //调用的该对象者的成员名
        [CallerMemberName]
        ``` 
    </br>

    4. ### 外部Dll包函数
      该特性一般用来调用C或C++等dll包中写好的方法；

      需要引入命名空间：using System.Runtime.InteropServices

        ```C#
        [DllImport(@"E:/Codes/c++/test/debug/bin/test.dll")]
        public static extern int C_plusplus_Method(int a,int b);
        //假设该方法是c++某程序集中的方法
        ``` 
</br>
</br>
</br>

* ## 自定义特性和反射获取特性信息
    
```C#
    public class MyCustomAttribute : Attribute//特性类
    {
        public string info;

        public MyCustomAttribute(string info) => this.info = info;

        public void CustomMethod()
        {
            Console.WriteLine("特性的方法");
        }
    }


    [MyCustom("这是一个类的特性")]
    public class Test//测试类
    {
        [MyCustom("这是一个成员变量的特性")]
        public int num;

        [MyCustom("这是一个方法的特性")]
        public void Method([MyCustom("这是一个方法参数的特性")] int num){}
    }


    public class MainTest//main调试
    {
        public static void Main(string[] args)
        {
            //通过反射获取Test各个元数据的特性


            //获取元数据f
            Test test = new Test();
            Type classType = test.GetType();

            FieldInfo fieldinfo = classType.GetField("num");
            MethodInfo methodinfo = classType.GetMethod("Method");
            ParameterInfo[] parameterinfo = methodinfo.GetParameters();

            //获得特性
            MyCustomAttribute classAttribute = classType.GetCustomAttribute(typeof(MyCustomAttribute)) as MyCustomAttribute;
            MyCustomAttribute fieldAttribute = fieldinfo.GetCustomAttribute(typeof(MyCustomAttribute)) as MyCustomAttribute;
            MyCustomAttribute methodAttribute = methodinfo.GetCustomAttribute(typeof(MyCustomAttribute)) as MyCustomAttribute;
            MyCustomAttribute[] parameterAttributes = new MyCustomAttribute[1];
            int i = 0;
            foreach (ParameterInfo p in parameterinfo)
            {
                parameterAttributes[i]= p.GetCustomAttribute(typeof(MyCustomAttribute)) as MyCustomAttribute;
            }

            //输出自定义特性中的info
            Console.WriteLine(classAttribute.info);
            Console.WriteLine(fieldAttribute.info);
            Console.WriteLine(methodAttribute.info);
            foreach (MyCustomAttribute c in parameterAttributes)
            {
                Console.WriteLine(c.info);
            }

            // 输出：
            // 这是一个类的特性
            // 这是一个成员变量的特性
            // 这是一个方法的特性
            // 这是一个方法参数的特性
        }
    }
```