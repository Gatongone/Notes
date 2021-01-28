# C#特性(Attribute)

   特性是一种能用于为元数据添加额外信息的类，可以用通过方括号([])的方式在元素（可以是类、接口、成员、函数、函数参数等）前声明标签。标签则代表该元素拥有该特性。之后可以通过反射的方式获取特性。
 
* ## 标签

    标签的本质其实是构造函数，写在元素的上一行表示他们具有该特性的信息。如果特性类名是XXXAttribute，那么在掉用标签时会自动省略Attribute，比如  [XXX("it is a string",5)] 。

* ## 常用的方法
    * 判断是否使用了某特性
    ```C#
    //memberinfo:元素据
    //参数1：是哪一种特性的类型
    //参数2：是否查找继承链（父类有这个特性算不算）
    //返回一个布尔值
    memberinfo.IsDefined(typeof(attribute),false);

    ```
    * 获得特性
    ```C#
    //获得元素据的全部特性
    //参数：是否查找继承链
    MyCustomAttribute[] mca = memberinfo.GetCustomAtrributes(true);
    
    //获得元素据的某个特性
    //参数为指定特性类型
    //MyCustomAttribute 为自定义特性
    MyCustomAttribute mca = memberinfo.GetCustomAttribute(typeof(MyCustomAttribute)) as MyCustomAttribute;
    ```

* ## 自定义特性
    
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
            //通过反射获取Test各个元素据的特性


            //获取元素据
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