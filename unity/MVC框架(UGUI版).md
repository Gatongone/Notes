# MVC（UGUI）

* ## Model
    向外部提供修改数据的方法
  ```C#
    public sealed class PlayerModel
    {

        //PlayerModel单例
        private static readonly Lazy<PlayerModel> _instance = new Lazy<PlayerModel>(() => new PlayerModel());

        public static PlayerModel Instance { get { return _instance.Value; } }

        private PlayerModel()
        {
            this.name = PlayerPrefs.GetString("PlayerName", "未命名");
            this.level = PlayerPrefs.GetInt("PlayerLevel", 1);
            this.diamondNum = PlayerPrefs.GetInt("PlayerDiamondNum", 10);
            this.goldNum = PlayerPrefs.GetInt("PlayerGoldNum", 5000);
            this.power = PlayerPrefs.GetInt("PlayerPower", 110);
            this.att = PlayerPrefs.GetInt("PlayerAtt", 120);
            this.def = PlayerPrefs.GetInt("PlayerDef", 30);
            this.spd = PlayerPrefs.GetInt("PlayerSpd", 60);
            this.hp = PlayerPrefs.GetInt("PlayerHp", 1200);

            EventManager.Instance.AddEventListener("update_playerData", saveData);
            EventManager.Instance.AddEventListener("LevelUp", LevelUp);
        }

        //字段数据
        private string name;
        private int level;
        private int diamondNum;
        private int goldNum;
        private int power;
        private int att;
        private int def;
        private int spd;
        private int hp;

        //属性
        public string Name { get => name; set => name = value; }
        public int Level { get => level; set => level = level > 0 ? 0 : value; }
        public int DiamondNum { get => diamondNum; set => diamondNum = diamondNum > 0 ? 0 : value; }
        public int GoldNum { get => goldNum; set => goldNum = goldNum > 0 ? 0 : value; }
        public int Power { get => power; set => power = power > 0 ? 0 : value; }
        public int Att { get => att; set => att = att > 0 ? 0 : value; }
        public int Def { get => def; set => def = def > 0 ? 0 : value; }
        public int Spd { get => spd; set => spd = spd > 0 ? 0 : value; }
        public int Hp { get => hp; set => hp = hp > 0 ? 0 : value; }

        //保存数据
        public void saveData()
        {
            PlayerPrefs.SetString("PlayerName", this.name);
            PlayerPrefs.SetInt("PlayerLevel", this.level);
            PlayerPrefs.SetInt("PlayerDiamondNum", this.diamondNum);
            PlayerPrefs.SetInt("PlayerGoldNum", this.goldNum);
            PlayerPrefs.SetInt("PlayerPower", this.power);
            PlayerPrefs.SetInt("PlayerAtt", this.att);
            PlayerPrefs.SetInt("PlayerDef", this.def);
            PlayerPrefs.SetInt("PlayerSpd", this.spd);
            PlayerPrefs.SetInt("PlayerHp", this.hp);

        }
        private void LevelUp()
        {
            Debug.Log("升级了");

            this.level += 1;
            this.power = 100;
            this.att += level * 7;
            this.def += level * 3;
            this.spd += level * 2;
            this.hp += level * level;

            EventManager.Instance.InvokeEvent("update_playerData");
        }
        
    }

  ```
</br>
</br>
</br>
</br>
</br>

* ## View
    向外部提供修改UI的方法

  * ### 接口  
    ```C#
        public interface IView
        {
            void UpdateInfo(PlayerModel data);
        }
    ```



  * ### View示例1
    ```C#
        public class MainView : MonoBehaviour,IView
        {
            public Button skill_btn;
            public Button roll_btn;

            public Text playerName_txt;
            public Text playerLevel_txt;
            public Text playerDiamond_txt;
            public Text playerGold_txt;
            public Text playerPower_txt;

            public void UpdateInfo(PlayerModel data)
            {
                playerName_txt.text = data.Name.ToString();
                playerLevel_txt.text = data.Level.ToString();
                playerDiamond_txt.text = data.DiamondNum.ToString();
                playerGold_txt.text = data.GoldNum.ToString();
                playerPower_txt.text = data.Power.ToString();
            }
        }
    ``` 

  * ### View示例2
    ```C#
    public class RoleView : MonoBehaviour,IView
      {
          public Button close_btn;
          public Button levelUp_btn;
          public Text playerLevel_txt;
          public Text playerAtt_txt;
          public Text playerDef_txt;
          public Text playerSpd_txt;
          public Text playerHp_txt;

          public void UpdateInfo(PlayerModel data)
          {
              playerLevel_txt.text = data.Level.ToString();
              playerAtt_txt.text = data.Att.ToString();
              playerDef_txt.text = data.Def.ToString();
              playerSpd_txt.text = data.Spd.ToString();
              playerHp_txt.text = data.Hp.ToString();
          }
      }
    ```

</br>
</br>
</br>
</br>
</br>

* ## Controller
    控制View和Model
  
  * ### 抽象控制器基类
    ```C#
        public abstract class BaseController<Controller, View> : MonoBehaviour
        where Controller : BaseController<Controller, View>,new()
        where View : IView, new()
        {
            //单例
            private static volatile Controller instance;
            private static object initLock = new object();

            public static Controller Instance
            {
                get
                {
                    if (instance == null)
                    {
                        throw new ArgumentNullException($"未初始化{nameof(Controller)}控制器");
                    }
                    else
                        return instance;
                }
            }

            public static void Init(string ResourcePath, string CanvasName)
            {
                if (instance == null)
                {
                    lock (initLock)
                    {
                        if (instance == null)
                        {
                            //从预制体中加载
                            GameObject res = Resources.Load(ResourcePath) as GameObject;
                            GameObject obj = Instantiate(res);

                            //设置画布为父对象
                            obj.transform.SetParent(GameObject.Find(CanvasName).transform, false);

                            //加载到场景中的资源生成控制器
                            instance = obj.GetComponent<Controller>();
                        }
                    }
                }
            }

            protected View view;//需要被匹配的View

            //匹配View
            protected virtual void Start()
            {
                //视图更新监听玩家数据更新事件
                EventManager.Instance.AddEventListener("update_playerData", UpdateView);
                
                view = transform.gameObject.GetComponent<View>();
                view?.UpdateInfo(PlayerModel.Instance);
            }

            //视图显示否
            public static void ShowView()
            {
                instance?.gameObject.SetActive(true);
            }
            public static void HideView()
            {
                instance?.gameObject.SetActive(false);
            }
            //销毁UI
            public static void Destroy()
            {
                EventManager.Instance.RemoveEventListener("update_playerData", instance.UpdateView);
                Destroy(instance?.gameObject);
            }
            //更新视图——根据玩家数据
            protected virtual void UpdateView()
            {
                instance?.view.UpdateInfo(PlayerModel.Instance);
            }
        }
    ```

  * ### 控制器实例1
    ```C#
        public class MainController : BaseController<MainController, MainView>
        {
            void Start()
            {
                base.Start();
                //技能按钮
                view.skill_btn.onClick.AddListener(() => { });
                
                //视图按钮
                view.roll_btn.onClick.AddListener(ShowRoll);
            }
            private void ShowRoll()
            {
                RoleController.Init("UI/RolePanel","Canvas");
                RoleController.ShowView();
            }
        }
    ```

  * ### 控制器实例2
    ```C#
    public class RoleController : BaseController<RoleController, RoleView>
        {
            private void Start()
            {
                base.Start();
                //关闭按钮事件
                view.close_btn.onClick.AddListener(() =>
                {
                    RoleController.Destroy();
                });
                //升级按钮事件
                view.levelUp_btn.onClick.AddListener(() =>
                {
                    EventManager.Instance.InvokeEvent("LevelUp");
                });
            }
        }
    ```