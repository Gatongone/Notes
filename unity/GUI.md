# GUI（IMGUI）

全称：即时模式用户交互界面，是一种即见即所得的UI系统，一般用作创建游戏内置工具和脚本检视面板而不作为游戏玩家的UI。

</br>

  * ## OnGui()
    继承了MonoBehaviour的脚本都可以调用OnGui()。OnGui()中的控件绘制函数都是由GUI类提供的静态函数，这些空间的的常见参数类型:

    * 位置参数：Rect(x ,y ,w ,h);（x，y位置信息；w，h尺寸信息）
    * 显示文本：string
    * 图片信息：Texture
    * 组合信息：GUIContent: 支持文本、图片、隐藏文本（鼠标或键盘选中时可获得该文本）
    * 自定义样式：GUIStyle

    </br>
    </br>

  * ## 常见的控件：

    <details>
    <summary><font size= 3>Label 标签</font></summary>

    ```C#
    void OnGUI()
    {
        GUI.Label(new Rect(0, 0, 100, 20), "Lable");
    }
    ```

    </details>

    <details>
    <summary><font size= 3>Button 按钮</font></summary>

    ```C#
    void OnGUI()
    {
        //按钮的事件监听

        //普通按钮（在Button范围内按下并抬起才算一次点击）
        if (GUI.Button(new Rect(0, 0, 100, 20), "button"))
        {
            Debug.Log("点击了按钮");
        }

        //重复按钮（按下时就会触发，长按时一直触发）
        if (GUI.RepeatButton(new Rect(0, 0, 100, 20), "button"))
        {
            Debug.Log("点击了重复按钮");
        }
    }
    ```
    </details>

    <details>
    <summary><font size= 3>Toggle 状态切换开关</font></summary>

    ```C#
    private bool isClick = false;
    private int selectIndex = 1;
    void OnGUI()
    {
        //可多选：
        //若isClick是true，则表示已经选择，点击后GUI.Toggle返回false（表示未启用）
        isClick = GUI.Toggle(new Rect(0, 0, 100, 20), isClick, "多选测试");

        //多选一：
        if (GUI.Toggle(new Rect(0, 30, 100, 20), selectIndex == 1, "单选测试1"))
        {
            selectIndex = 1;
        }
        if (GUI.Toggle(new Rect(0, 30+20, 100, 20), selectIndex == 2, "单选测试2"))
        {
            selectIndex = 2;
        }
        if (GUI.Toggle(new Rect(0, 30+40, 100, 20), selectIndex == 3, "单选测试3"))
        {
            selectIndex = 3;
        }
    }
    ```
    </details>

    <details>
    <summary><font size= 3>TextField 输入框</font></summary>

    ```C#
    private string inputpwd;
    private string input;
    void OnGUI()
    {
        //参数中的input表示需要嵌入的字符串,GUI.Toggle将返回当前输入框内的字符串
        //常规输入
        input = GUI.TextField(new Rect(0, 0, 100, 20), input);

        //加密输入，最后一个参数为替换为密码的符号
        inputpwd = GUI.PasswordField(new Rect(0, 0, 100, 20), inputpwd, '*');
    }
    ```
    </details>

    <details>
    <summary><font size= 3>Slider 滑块</font></summary>

    ```C#
    private int value = 5;
    private int max = 10;
    private int min = 0;
    void OnGUI()
    {
        //水平滑块
        value = GUI.HorizontalSlider(new Rect(0, 0, 100, 20), value, min, max);
        //垂直滑块
        value = GUI.VerticalSlider(new Rect(0, 0, 100, 20), value, min, max);
    }
    ```
    </details>
    <details>
    <summary><font size= 3>DrawTexture 图片绘制</font></summary>

    ```C#
    private Texture tex;
    private ScaleMode  scaleMode;
    public bool alpha;
    public float imageAspect;
    void OnGUI()
    {
        // scaleMode 缩放模式:
        //         Scale To Fill 任意拉伸
        //         Scale And Crop 保持比例，多出的部分裁剪
        //         Scal To Fit 保持比例
        //alpha 是否启用alpha通道
        //imageAspect 宽高比，默认0表示图片的原始比例
        GUI.DrawTexture(new Rect(0, 0, 100, 100), tex, scaleMode, alpha, imageAspect);
    }
    ```
    </details>    
    <details>
    <summary><font size= 3>Box 容器</font></summary>

    ```C#
    void OnGUI()
    {
        GUI.Box(new Rect(0, 0, 100, 100), " ");
    }
    ```
    </details> 

    <details>
    <summary><font size= 3>Toolbar 工具栏</font></summary>

    ```C#
    private int toolbarIndex = 0;
    private string[] toolbarInfos = new string[] { "工具一", "工具二", "工具三" };
    void OnGUI()
    {
        //toolbarIndex:点击哪个toolbarInfos将返回数组下标，表示选择了哪个工具栏
        toolbarIndex = GUI.Toolbar(new Rect(0, 0, 200, 30), toolbarIndex, toolbarInfos);

        switch (toolbarIndex)
        {
            case 0: Debug.Log("选择了工具一"); break;
            case 1: Debug.Log("选择了工具二"); break;
            case 2: Debug.Log("选择了工具三");break;
        }
    }
    ```
    </details>

    <details>
    <summary><font size= 3>SelectionGrid 选择网格</font></summary>

    ```C#
    private int selectionIndex = 0;
    private string[] SelectionInfos = new string[] { "工具一", "工具二", "工具三" };
    void OnGUI()
    {
        //与Toolbar类似，但Toolbar只能支持一行
        //最后一个参数表示每行最多能有几个工具栏，多余的工具栏将置于下一行
        selectionIndex = GUI.SelectionGrid(new Rect(0, 0, 200, 60), selectionIndex, SelectionInfos, 1);

        switch (selectionIndex)
        {
            case 0: Debug.Log("选择了工具一"); break;
            case 1: Debug.Log("选择了工具二"); break;
            case 2: Debug.Log("选择了工具三");break;
        }
    }
    ```
    </details>

    <details>
    <summary><font size= 3>Group 分组</font></summary>

    ```C#
    public Rect groupPos;
    void OnGUI()
    {
        //可以批量控制控件显示范围和位置

        GUI.BeginGroup(groupPos);

        GUI.Label(new Rect(0, 0, 100, 20), "Lable");
        GUI.Button(new Rect(0, 20, 100, 20), "Button");

        GUI.EndGroup();
    }
    ```
    </details>

    <details>
    <summary><font size= 3>ScrollView 滚动视野</font></summary>

    ```C#   
    public Rect scrPos;//滚动组位置和范围
    public Rect showPos;//可视范围
    private Vector2 nowPos;//当前滚动视野位置

    private int selectionIndex = 0;
    private string[] SelectionInfos = new string[] { "工具一", "工具二", "工具三" };

    void OnGUI()
    {
        //showPos > scrPos 时将出现滚动滑块

        nowPos = GUI.BeginScrollView(scrPos, nowPos, showPos);

        selectionIndex = GUI.SelectionGrid(new Rect(0, 0, 200, 60), selectionIndex, SelectionInfos, 1);

        GUI.EndScrollView();
    }
    ```
    </details>

    <details>
    <summary><font size= 3>Window 窗口</font></summary>

    ```C#
    private bool isclose1 = true;
    private bool isclose2 = true;
    private bool isclose3 = true;
    public Rect dragPos;
    public GUIStyle style;

    void OnGUI()
    {
        //第三个参数均为委托函数，用于处理窗口内的逻辑

        //普通窗口
        if(isclose1)
            GUI.Window(1, new Rect(0, 0, 200, 60), DrawWindow, "普通窗口");

        //模态窗口，必须优先处理模态窗口的逻辑，模态窗口启用时，其他所有GUI控件失效
        if(isclose2)
            GUI.ModalWindow(2, new Rect(0, 200, 200, 60), DrawWindow, "模态窗口");

        //可拖动窗口
        if(isclose3)
            dragPos = GUI.Window(3, dragPos, DrawWindow, "可拖动窗口");

    }
    public void DrawWindow(int id)
    {
        //按钮关闭窗口
        if (GUI.Button(new Rect(180, 0, 20, 20),"X",style))
        {
            switch (id)
            {
                case 1: isclose1 = !isclose1; break;
                case 2: isclose2 = !isclose2; break;
                case 3: isclose3 = !isclose3; break; 
            }
        }

        //可拖动窗口必须在委托函数内部调用GUI.GragWindow()
        if (id == 3)
        {
            //参数表示能拖拽的范围，无参则窗口内范围均可拖拽
            //GUI.DragWindow(new Rect(0, 0, 100, 20));
            GUI.DragWindow();
        }
    }    
    ```
    </details>
    </br>
    每一种控件都有多个重载，但都必须有位置信息和显示信息 

    ps：OnGui()是每帧执行的。

    </br>
    </br>

  * ## 全局颜色和自定义皮肤样式
    
    * ### 全局颜色
    GUI中提供了全局颜色，将OnGUI中所有的控件进行颜色的叠加：
        GUI.color 全局颜色
        GUI.contentColor 文本颜色
        GUI.backgroundColor 背景颜色
    
    ![alt](https://raw.githubusercontent.com/Gatongone/ImageContainer/main/Others/guicolor.png)
    ```C#
    void OnGUI()
    {
        GUI.color = Color.red;//设置一个全局颜色

        GUI.Button(new Rect(0, 0, 100, 20), "Button");
        GUI.Label(new Rect(0, 30, 100, 20), "Lable");

        GUI.contentColor = Color.blue;//与红色叠加文本呈黑色
        GUI.backgroundColor = Color.blue;//与红色叠加背景呈黑色
        GUI.Button(new Rect(0, 60, 100, 20), "Button");
        GUI.Label(new Rect(0, 90, 100, 20), "Lable");

        GUI.color = Color.white;//清空全局颜色，但是文本和背景颜色未清空
        GUI.Button(new Rect(0, 120, 100, 20), "Button");
        GUI.Label(new Rect(0, 150, 100, 20), "Lable");

    }
    ```
    * ### 自定义皮肤样式
    可以在project面板下创建GUI Skin,GUI Skin其实是各个组件的综合体，相较于GUIStyle一般作为一整套的皮肤应用于各个控件，可以声明为public对象并将其拖拽至脚本上。

    GUI.skin 将会与下面的控件相关联，控件将获得GUI SKin里边的样式。

    ps：GUI Skin 将会与 GUI.Style 冲突
    ```C#
    public GUISkin skin;
    void OnGUI()
    {
        GUI.skin = skin;//下面的button和label将获得skin的皮肤样式

        GUI.Button(new Rect(0, 0, 100, 20), "Button");
        GUI.Label(new Rect(0, 30, 100, 20), "Lable");

        GUI.skin = null;//skin被清空，lebel变为默认的样式

        GUI.Label(new Rect(0, 150, 100, 20), "Lable");
    }
    ```
    
    </br>
    </br>

  * ## GUILayout 自动布局
    GUILayout同样拥有Label、Button等GUI控件，不同的是，GUILayout不需要去声明控件的大小和位置，它将会根据内容进行自适应填充，并根据控件组中最长的那个控件进行长宽的扩展。

    GUILayout独有的参数类型：GUILayoutOption。返回类型为GUILayoutOption的GUILayout方法有以下：

    固定长宽
      * GUILayout.Width(float);
      * GUILayout.Height(float;
 
    最小长宽
      * GUILayout.MinWidth(float);
      * GUILayout.MinHeight(float);

    最大长宽
      * GUILayout.MaxWidth(float);
      * GUILayout.MaxWidth(float);

    是否允许自动扩展控件长宽
      * GUILayout.ExpandWidth(bool);
      * GUILayout.ExpandHeight(bool);
  
  </br>
    GUILayout还可以通过下面的方式控制布局
      *  批量控制组件（类似于Group）：GUILayout.BeginArea()、GUILayout.EndArea()
      *  水平的布局：GUILayout.BeginHorizontal()、GUILayout.EndHorizontal()
      *  垂直的布局：GUILayout.BeginVertical()、GUILayout.EndVertical()