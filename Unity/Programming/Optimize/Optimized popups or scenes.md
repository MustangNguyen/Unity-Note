Need 2 dictionary:
- One for name and load path.
- One for name and popup game object.
Name should use enum.
Can use a list game object to manage active scene.
``` cs
 private Dictionary<UIName, UIBase> cacheScreen = new Dictionary<UIName, UIBase>();

        /// <summary>

        /// 0: UiController, shop, event, main, guild, free (on tab)

        /// 1: General (down top)

        /// 2: top, changeScene,login, ....

        /// 3: loading, NotiUpdate

        /// top: -1: not action || -2: Hide top || >=0: Show top

        /// </summary>

        private Dictionary<UIName, string> dir = new Dictionary<UIName, string>

    {

            // UIName, rootIdx,topIdx,loadPath

            {UIName.Gameplay, "0,0,UIGameplay"},

            {UIName.Setting,"0,0,UISetting" },

            {UIName.Onboarding,"0,0,UIOnboarding" },

            {UIName.Main,"0,0,UIMain" },

            {UIName.ARFilterSelection,"0,0,UIARFilterSelection" },

            {UIName.CameraView,"0,0,UICameraView" },

            {UIName.ARVideoSelection,"0,0,UIARVideoSelection" },

            {UIName.PreviewVideo,"0,0,UIPreviewVideo" },

            {UIName.PreviewPicture,"0,0,UIPreviewPicture" },

            {UIName.SelectLanguage,"0,0,UISelectLanguage" },

            {UIName.Settings,"0,0,UISettings" },

            {UIName.Rate,"0,0,UIRate" },

            {UIName.GetPremium,"0,0,UIGetPremium" },

            {UIName.Collection,"0,0,UICollection" },

            {UIName.Policy,"0,0,UIPolicy" },

            {UIName.Disclaimer,"0,0,UIDisclaimer" },

        };
```
