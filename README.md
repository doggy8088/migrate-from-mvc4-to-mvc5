migrate-from-mvc4-to-mvc5
=========================

本專案示範從 ASP.NET MVC 4 升級到 ASP.NET MVC 5 的完整過程。

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

    ```
    git add .
    git commit -m "Step 1: Re-targeting to .NET Framework 4.5.1"
    ```

2. Uninstall unused packages

    ```
    git add .
    git commit -m "Step 2: Uninstall the following NuGet packages"
    ```

3. Install the renamed packages

    ```
    git add .
    git commit -m "Step 3: Install the Microsoft.AspNet.WebHelpers NuGet packages"
    ```

4. Update all the packages

    ```
    git add .
    git add -u .
    git commit -m "Step 4: Update all the NuGet packages to the latest version"
    ```

5. web.config

    ```
    git add .
    git commit -m "Step 5: Update the web.config"
    ```

6. Views\web.config

    ```
    git add .
    git commit -m "Step 6: Update the Views\web.config"
    ```

7. web.config

    ```
    git add .
    git commit -m "Step 7: Update the web.config for User.IsInRole() usage"
    ```

8. ProjectTypeGuids

    ```
    git add .
    git commit -m "Step 8: Remove the ASP.NET MVC 4's ProjectTypeGuids in the project file"
    ```

9. Fixed the namespace conflict

    ```
    git add .
    git commit -m "Step 9: Fixed the error CS0104 problem due to namespace conflict"
    ```

###發生升級失敗的時候，可透過以下指令還原變更：

```
git reset --hard && git clean -x -f -d
```

與我聯絡
---------

* 粉絲頁: [Will 保哥的技術交流中心](https://www.facebook.com/will.fans)
* 部落格: [The Will Will Web](http://blog.miniasp.com/)
* 噗　浪: [★★★ Will 保哥的噗浪 ★★★](http://www.plurk.com/willh/invite)
