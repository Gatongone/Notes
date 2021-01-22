# C#各版本新增语法

* ## C#6
     * 自动属性或方法表达式
     
        ```C#
        class Person
        {
            private string FirstName,LastName;

            //属性
            public string name => $"{FirstName} —— {LastName}";


            //方法

            //原来的
            public string getName()
            {
                return $"{FirstName} —— {LastName}";
            } 
            //使用goes to表达式
            public string getName() => $"{FirstName} —— {LastName}";
        }
        ```
    </br>

    * using  static namespace
    
      如果引用命名空间时使用static关键字，那么对该命名空间中的静态方法可以直接调用而不需要使用 类名.方法();的写法

      ```C#
      using  static MarkDown.Code.TestClass;

      namespace MyNamespace
      {
        public class MyClass
        {
            Method();//假设该方法是MarkDown.Code命名空间中TestClass类中的一个静态方法
        }   
      }
      ```
      </br>

    * ？：null条件运算符
        
        Nullable< T >的简写，int? 相当于Nullable< int >
        
        注意：？修饰的必须是可为null的类型,空值与任何值的运算结果均为空

      ```c#
        int x1 = default(int);//默认值为0
        int? x2 = default(int?);//默认值为null
        int? x3 = x2 + 1;//输出仍然为null
      ```  
      
        null条件运算符执行顺序为如果student不为空则返回 student. name，否则返回null

      ```c#
      public class Student
      {
          public string name;
          public int? id;
          public
      }

      public class Test()
      {
          public static void Main(string[] args)
          {
              Student student =null;
              string name;

              //原来的写法
              if(student != null)
                    name = student.name;


              //使用null条件运算符后：
              name = student?.name;       
              
              int? id = student?.id//必须为可空类型服务
              int id = student?.id//报错
          }
      }
      ```
      同样可以用在方法中
      ```C#
      public class Point
      {
          public int? x;
          public int? y;
      }

      public class Coordinate
      {
          private Point point = new Point();
          public Point getPoint()
          {
              return point;
          }
      }

      public class MainTest
      {
          static void Main(string[] args)
          {
              Coordinate coordinate =new Coordinate();

              int? x = coordinate?.getPoint()?.x;

              //相当于
              int? x;
              if(coordinate!=null)
                  if(coordinate.getPoint()!=null)
                      x = coordinate.getPoint().x;
          }
      }
 
      ```
    </br>
    
    * 字符串的内插
  
      内插的引入很好的解决了Format所带来的参数麻烦问题（参数值个数必须一致、顺序必须一致，占位符必须一致）
      ```c#
      int a=1,b=2,c=3;

      //原来的写法
      string.Format("{0} + {1} = {2}", a, b, c);

      //内插的写法
      string str = $"{a} + {b} = {c}";

      //利用内插在字符串中进行运算
      string str2 = $"s={a+b}"; //输出 s=3
      ```
    </br>
    
    * nameof 操作符
  
      nameof的作用是接收元素后返回元素名字。这个操作符能将class和class的所用成员，比如方法、变量以及属性作为参数而后返回一个它们的名字。这避免我们在代码中hardcode字符串( 比如可以用#defined PI 3.14159265 的程序直接写x = 3.14159265; 而不用x = PI; )，也避免使用反射来获得这些名字。

      ```c#
        class Program
        {
            static void Main(string[] args)
            {
                WriteLine(nameof(Student));//输出: Student
                WriteLine(nameof(Student.Name));//输出: Name;
            }
        }
        
        class Student
        {
            public string Name { get; set; }
        }
      ```
    </br>
    
    * 使用索引器初始化关联集合
      
      比如初始化字典时可以用

      ```c#
      Dictionary<int string> dictionary=new Dictionary<int,string>
      {
          {1,"this is fitst string"},
          {2,"this is second string"}
          {3,"this is third string"}
      };
      ```
    </br>
    </br>
    </br>
    </br>
* ## C#7
    * ref 和 out 变量
   
      ref和out的共同点：
        * 都能返回多个返回值
        * 都能将值类型转换成引用类型
        * 对于CLR来说，使用out和ref都会生成相同的IL代码。
  
      ref和out的区别是：
        * ref：参数在调用前必须初始化，可以将参数的数值传递进函数；
        * out：参数在调用前可以不初始化，但是会把参数清空，在未被赋予新值之前是未初始化的状态
      ```C#
      public class MainTest
      {
          static void Main(string[] args)
          {
              int x=5;
              int y=3;
              int n1,n2;

              SwapValue( x , y );
              Console.WriteLine($"x={x},y={y}");//输出 x=5,y=3

              SwapReference_ref( x , y );
              Console.WriteLine($"x={x},y={y}");//输出 x=3,y=5

              SwapReference_out( x , y );//报错
              SwapReference_ref( n1 , n2 );//报错，因为ref不能对未赋值的变量进行操作
          }
          public static void SwapValue(int a,int b)//值传递，a和b只是获得了x，y的值，并没有获得x，y的地址
          {
              int temp = a;
              a = b;
              b = temp;
          }
          public static void SwapReference_ref(ref int a,ref int b)
          {
              int temp = a;
              a = b;
              b = temp;
          }
          public static void SwapReference_out(out int a,out int b)
          {
              int temp = a;//报错，使用了未赋值的参数a，因为传入了a的引用，但是引用被out清空了
              a = b;
              b = temp;
          }
      }
      ```