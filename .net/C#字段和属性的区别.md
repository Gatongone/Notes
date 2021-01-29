# C#字段和属性的区别

在C#中字段（成员变量Field）和属性（Property）都作为类成员存在，在类的设计原则中，字段往往被用作私有成员供类内部使用，而属性一般作为访问字段供外部调用，这提供了良好的封装性；

属性的get或set可以不写某一个表示只可读或只可取，但不能都不写；

由于访问器的存在，属性可以对值（隐式的参数value）进行验证和处理；

ps：ref和out关键字只能对字段使用，而属性不能。
   
```C#
    // 字段
    private string name;
    private int age;
    private string sex;
    // 属性
    public string Name
    {
        get 
        {
            if(sex.Contains(""))
                return "未命名"
            return name; 
        }
        set;
    }
    public int Age
    {
        get ;
        set 
        {
            if (value<0)
                age =0;   
            else
                age = value; 
        }
    }
    public string Sex
    {
        get ;
        set 
        {
            if (value!="男"||"女")
                sex = "无性别";
            else    
            sex = value; 
        }
    }
```        