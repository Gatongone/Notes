# unity基本工作原理

## .meta: 资源的引用

![alt](https://raw.githubusercontent.com/Gatongone/ImageContainer/main/Others/MetaFile.png)

当导入资源或者创建资源时将自动生成一个.meta文件。

打开后是一个yaml的序列化文件

![alt](https://raw.githubusercontent.com/Gatongone/ImageContainer/main/Others/.meta.png)

在unity中引用一个游戏资源，并非是通过路径的方式，而是通过一个GUID来指向工程里的该资源文件。因此即使移动、重命名或修改资源内容，资源依旧可以通过GUID来引用。（注意，当资源内有多个子文件，unity将为子文件分配一个FileID来区分每个子文件，其中资源间的依赖关系通过GUID来确定，资源内部的依赖关系使用fileID来确定）

在导入资源时unity将会完成以下事情：

1. 分配ID：检测资源列表，并为其分配一个唯一ID
2. 创建资源关联文件：创建.meta文件用于保存ID与资源的关联，而资源与资源间（GameObject与脚本）的关系会被保存在场景文件.unity文件中
3. 打包数据：将资源数据打包成unity运行时能识别的数据，unity将在内部维护一个缓存表，将GUID和fileID转换整数，即实例ID（InstanceID）

在unity运行时（不是游戏运行时）将根据实例ID所缓存的所有对象（包括Resource文件夹下的）一起被初始化，只有当实例ID失效（比如卸载AssetBundle）时才会从缓存中移出。

</br>
</br>
</br>

## .unity: 资源间的关系：
利用unity制作游戏本质上就是在一个运行中的软件中制作游戏，而运行中的软件之所以能读取到创建了哪些对象信息是因为引擎对场景的配置文件进行了读取。 当在Inspector面板上拖拽脚本和资源，会被.unity文件视为引用。因此.unity文件保存了场景中资源与资源之间的关系。

![alt](https://raw.githubusercontent.com/Gatongone/ImageContainer/main/Others/ScenesFile.png)

当保存场景后会在Assets文件夹下自动的生成.unity这样的一个场景文件。

打开后可以发现是一个yaml格式的场景配置文件（序列化文件）。

![alt](https://raw.githubusercontent.com/Gatongone/ImageContainer/main/Others/.unity.png)

在编辑器下，当创建一个MonoBehaviour脚本时，保存后自动生成程序集,并被分配ID。而在把脚本拖拽到GameObject上时，脚本与GameObject的关联信息将被保存在.unity中。
    
运行期间，Unity读取.unity文件中的信息，并根据ID与资源的关系，通过反射（类似于C#反射，但不是C#内置的）创建在Scene中看到的对象。

* 总结：
  脚本文件就像一个个剧本，而GameObject就是一个个演员，其中每个演员都有一个固定的Transform的剧本，演员和剧本的关系被保存在了.unity文件中
