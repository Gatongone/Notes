# C#反射

* ## 程序集和元数据
  * 程序集：.net中由C#、VB等语言的编译器编译后所产生的代码集合（包含了CIL）,一般被存放在bin文件中，后缀名一般为.dll（库文件）、.exe（可执行文件）。别人可以通过反射调用其他人的程序集的元数据。
  * 元数据：用来描述数据的数据，本质上就是用于描述被编译成程序集之前的各种类、成员等信息，元数据是保存在程序集中的。

    </br>
    </br>

* ## 反射
  在程序运行时，可以获得其他程序集或自身程序集中的元数据，该过程就叫反射。

  也就是说通过反射可以时，可以使用其他程序集中的类、变量、特性等，而不用自己实现。

    </br>
    </br>

* ## 三种关键类型:
  * ### Type:
    表示某一类型并存储其信息，在反射中用于获取类的相关信息（构造函数、方法、成员变量、属性、事件等），是访问元数据的主要方式。

    获取Type的方法主要有
    ```C#
        //通过object中的GetType方法获取
        int i = 20;
        Type type1 = i.GetType();

        //通过typeof关键字传入类型
        Type type2 = typeof(int);

        //通过传入类型的名字,注意需要将命名空间一并传入
        Type type3 = Type.GetType("System.Int32");
    ```
    通过反射获取的类型（公有类型）：
    \ |类型|获得方法|获得数组方法
    -----|-----|-----|-----
    所有公共成员|MemberInfo|Type.GetMember(Type type)|Type.GetMembers(Type type)
    构造函数|ConstructorInfo|Type.GetConstructor(Type type)|Type.GetConstructors(Type type)
    成员变量|FieldInfoInfo|Type.GetField(Type type)|Type.GetFields(Type type)
    成员方法|MethodInfo|Type.GetMethod(Type type)|Type.GetMethods(Type type)
    属性|PropertyInfo|Type.GetProperty(Type type)|Type.GetPropertys(Type type)
    事件|EventInfo|Type.GetEvent(Type type)|Type.GetEvents(Type type)
    枚举|EnumInfo|Type.GetEnumName(Type type)|Type.GetEnumNames(Type type)
    接口|InterfaceInfo|Type.GetInterface(Type type)|Type.GetInterfaces(Type type)

    </br>

  * ### Assembly:
    程序集类，用于加载程序集，一般用于加载非本程序集。

    一般有三种加载方式：
    ```C#
        Assembly assembly1 = Assembly.Load("当前文件夹下的程序集名称");

        Assembly assembly2 = Assembly.LoadFrom("包含程序集清单的文件路径(一般是dll)");

        Assembly assembly3 = Assembly.LoadFile("某个程序集的绝对路径");
    ```

    </br>

  * ### Activator:
    用于快速实例化Type对象,可以根据参数快速匹配构造函数，不需要获取ConstructorInfor再去Invoke。用法：

    ```C#
        //Test为当前程序集中的一个类
        Type classType = typeof(Test);

        //无参构造
        Object obj1 = Activator.CreateInstance(classType);

        //有参构造
        Object obj2 = Activator.CreateInstance(classType ,参数1 ,参数2);
    ```

    </br>

* ## 反射过程

    假设在某个程序集中有

    ```C#
    namespace Markdown.Codes
    {
        public class Test
        {
            //私有成员变量，无法被反射
            private int num = 20;
            //公有成员变量
            public string name;
            //属性
            public int Num
            {
                get
                {
                    Console.WriteLine("获取属性");
                    return num;
                }
                set
                {
                    Console.WriteLine("填充属性");
                    num = value;
                }
            }
            //有参构造函数
            public Test(int num, string name)
            {
                Console.WriteLine("使用了有参构造函数");
                this.num = num;
                this.name = name;
            }
            //无参构造函数
            public Test()
            {
                Console.WriteLine("使用了无参构造函数");
            }
            //成员方法
            public void Method()
            {
                Console.WriteLine("使用了方法");
            }
        }
    }
    ```

    在另一程序集中对其反射
    ```c#
    public class MainTest
    {
        static void Main(string[] args)
        {
            //获得程序集,如果是当前程序集则不需要
            Assembly assembly = Assembly.LoadFrom(@"E:\khaki\Markdown\bin\Debug\net5.0\Markdown");



            //获得类
            //其他程序集
            Type classType = assembly.GetType("Markdown.Codes.Test");
            //当前程序集
            //Type classType = Type.GetType("Markdown.Codes.Test");



            //获得无参构造函数,参数为形参类型没有则填new Type[0]，不能填null
            ConstructorInfo ctorinfo1 = classType.GetConstructor(new Type[0]);
            //执行构造函数
            Object obj1 = ctorinfo1.Invoke(new object[0]);//参数为实参，没有填null或new object[0]
            //或直接执行并赋值
            obj1 = Activator.CreateInstance(classType);



            //获得有参构造函数,参数为形参类型
            ConstructorInfo ctorinfo2 = classType.GetConstructor(new Type[] { typeof(int), typeof(string) });
            //执行构造函数
            Object obj2 = ctorinfo2.Invoke(new object[] { 55, "关东关" });
            //或直接执行并赋值
            obj2 = Activator.CreateInstance(classType, 55, "关东关");



            //获得成员变量
            FieldInfo fieldInfo = classType.GetField("name");
            //为字段赋值
            //参数1：被赋值的对象
            //参数2：赋值的值
            //相当于obj2.name="啊萨德";
            fieldInfo.SetValue(obj2, "啊萨德");
            //获取字段
            //参数为对象名
            //相当于obj2.name;
            Console.WriteLine(fieldInfo.GetValue(obj2));



            //获得方法
            //参数1：方法名
            //参数2：为形参类型
            MethodInfo methodInfo = classType.GetMethod("Method", new Type[0]);
            //执行方法
            //参数1：为执行该方法的对象，如果是静态方法则填null或new object[0]
            //参数2：为实参，没有则填null
            //相当于obj2.Method();
            methodInfo.Invoke(obj2, null);



            //获得属性
            PropertyInfo propInfo = classType.GetProperty("Num");
            //参数1：被赋值的对象
            //参数2：赋值的值
            //相当于obj2.Num=80;
            propInfo.SetValue(obj2, 80);
            //获取字段
            //参数为对象名
            //相当于obj2.Num;
            Console.WriteLine(propInfo.GetValue(obj2));
        }
    }
    ```