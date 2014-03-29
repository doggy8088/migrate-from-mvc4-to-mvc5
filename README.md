migrate-from-mvc4-to-mvc5
=========================

本專案示範從 ASP.NET MVC 4 升級到 ASP.NET MVC 5 的完整過程。

操作步驟錄影
------------

如何從 ASP.NET MVC 4.0 升級到 ASP.NET MVC 5.0 或 5.1 版

[![ScreenShot](https://cloud.githubusercontent.com/assets/88981/2558150/14a46654-b736-11e3-92bd-f3ccaf8d6934.png)](https://www.youtube.com/watch?v=6j6W8WfD1Fk&hd=1)


升級步驟說明
-------------

請參見保哥於 2014/3/21 微軟技術關卡破解日的【ASP.NET MVC 5 新功能探索】演講內容。

簡報檔: [ASP.NET MVC 5 新功能探索](http://bit.ly/aspnet-mvc-5-whats-new) (SlideShare)

查看升級歷程的方式
-------------------

可利用 TortoiseGit 的 Git Show log 功能查看先前的分支版本，如下圖示：

![image](https://f.cloud.github.com/assets/88981/2482996/eef4dc7a-b0f7-11e3-995e-e24e0168f5b7.png)

或可直接從 GitHub 點擊 [commits](https://github.com/doggy8088/migrate-from-mvc4-to-mvc5/commits/master) 連結查看每個版本的紀錄。

ASP.NET MVC 4 升級 ASP.NET MVC 5/5.1 所有 Git 命令
---------------------------------------------------

###建立目前專案的 Git 儲存庫

```
git init
echo /packages> .gitignore
echo /*/bin>> .gitignore
echo /*/obj>> .gitignore
git config --local core.safecrlf false
git config --local core.autocrlf false
git add .
git commit -m "Initial commit"
```

###建立 migrate-to-mvc5 分支，用於紀錄升級的完整過程

```
git checkout -b migrate-to-mvc5
```

###開始升級專案

1. Re-targeting .NET Framework

    透過 Visual Studio 2013 修改 ASP.NET MVC 4 的專案屬性，把 Target Framework 切換到 4.5.1

    ```
    git add .
    git commit -m "Step 1: Re-targeting to .NET Framework 4.5.1"
    ```

2. Uninstall unused packages

    刪除下套件：
    * Uninstall-Package -Id Microsoft.AspNet.WebPages.Administration
    * Uninstall-Package -Id Microsoft.AspNet.Mvc.FixedDisplayModes
    * Uninstall-Package -Id Microsoft-Web-Helpers

    ```
    git add .
    git commit -m "Step 2: Uninstall the following NuGet packages"
    ```

3. Install the renamed packages

    安裝以下套件：
    * Install-Package -Id Microsoft.AspNet.WebHelpers

    ```
    git add .
    git commit -m "Step 3: Install the Microsoft.AspNet.WebHelpers NuGet packages"
    ```

4. Update all the NuGet packages

    執行 NuGet 更新所有套件
    * Update-Package

    注意事項
    * 注意 JavaScript 函式庫是否適合更新到最新版
        * Bootstrap  		2.3.2 --> 3.1.0
        * jQuery 			1.8.3 --> 1.11.0
        * jquery-globalize	0.1.0 --> 0.1.3  (路徑會變)
    * 更新的過程會將 ASP.NET MVC 4 升級到 ASP.NET MVC 5
        * 所有組件參考會在這個步驟自動完成更新
        * 更新的過程會更新部分 web.config 設定 (含組件版本)
    * 有些套件更新後，檔案路徑會變，可能要改 BundleConfig.cs
    * 缺乏繁體中文套件的錯誤可以忽略/或直接移除舊版繁中套件
    * 不小心更新到不該更新的套件怎麼辦
        * [使用NuGet更新套件時將jQuery升級到2.0.2應該如何降級](http://blog.miniasp.com/post/2013/06/10/Downgrading-jQuery-version-with-NuGet.aspx)
        * 範例：
            * Uninstall-Package jQuery -Force
            * Install-Package jQuery -Version 1.8.3
        * 善用版本控管比對 packages.config 更新前後的套件版本
    
    ```
    git add .
    git add -u .
    git commit -m "Step 4: Update all the NuGet packages to the latest version"
    ```

5. web.config

    更新 web.config 設定
    * 修正 &lt;appSettings&gt; 區段
        * &lt;add key="webpages:Version" value="3.0.0.0" /&gt;

    ```
    git add .
    git commit -m "Step 5: Update the web.config"
    ```

6. Views\web.config

    更新 Views\web.config 設定
    * 包含所有 Area 下的 Views\web.config
    * 修正 <system.web.webPages.razor> 區段
    * 修正 <system.web> 區段
        * 4.0.0.0 --> 5.0.0.0
        * 2.0.0.0 --> 3.0.0.0

    ```
    git add .
    git commit -m "Step 6: Update the Views\web.config"
    ```

7. web.config

    若你的網站有用到 User.IsInRole() 的話，必須移除 RoleManager 這個 HttpModules

    ```
    <system.webServer>
      <modules>
        <remove name="RoleManager" />
      </modules>
    </system.webServer>
    ```


    ```
    git add .
    git commit -m "Step 7: Update the web.config for User.IsInRole() usage"
    ```

8. ProjectTypeGuids

    更新 ASP.NET MVC 專案檔 ( *.csproj )
    * 找到 ProjectTypeGuids 設定
    * 刪除 {E3E379DF-F4C6-4180-9B81-6769533ABE47}; 這段

    ```
    git add .
    git commit -m "Step 8: Remove the ASP.NET MVC 4's ProjectTypeGuids in the project file"
    ```

9. Fixed the namespace conflict

    重新載入專案，並重新建置方案，看是否能成功編譯

    常見錯誤與解決方法
    * error CS0104: 'CompareAttribute' 參考在 'System.ComponentModel.DataAnnotations.CompareAttribute' 和 'System.Web.Mvc.CompareAttribute' 之間模稜兩可
    * 從 Models\*.cs 中註解所有 System.Web.Mvc 命名空間即可
        `// using System.Web.Mvc;`

    ```
    git add .
    git commit -m "Step 9: Fixed the error CS0104 problem due to namespace conflict"
    ```

###發生升級失敗的時候，可透過以下指令還原變更：

```
git reset --hard && git clean -x -f -d
```

相關連結
-------

* [How to Upgrade an ASP.NET MVC 4 and Web API Project to ASP.NET MVC 5 and Web API 2 : The Official Microsoft ASP.NET Site](http://www.asp.net/mvc/tutorials/mvc-5/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2)
* 演講簡報：[ASP.NET MVC 5 新功能探索](http://bit.ly/aspnet-mvc-5-whats-new) (SlideShare)
* 現場錄影：[ASP.NET MVC 5 新功能探索 - MVP 微軟技術關卡破解日](http://channel9.msdn.com/Events/MVP-Virtual-Conference/MVP-Comcamp-Taiwan-2014/ASP-NET-MVC-5-New-Features) (Channel 9)

與我聯絡
---------

* 粉絲頁: [Will 保哥的技術交流中心](https://www.facebook.com/will.fans)
* 部落格: [The Will Will Web](http://blog.miniasp.com/)
* 噗　浪: [★★★ Will 保哥的噗浪 ★★★](http://www.plurk.com/willh/invite)
