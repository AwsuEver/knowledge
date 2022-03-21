#### 关于git

1. ##### 安装git

   > windows上安装git，只需要从官网上选择合适的版本，下载后顺序执行即可
   >
   > 因为git是**分布式**系统，因此每台机器在安装完成git后都需要自报家门：用户**姓名**和**邮箱**。进行以下配置，来唯一标识你的相关信息
   >
   > ~~~shell
   > git config --global user.name="your name"
   > git config --global user.email="your email"
   > ~~~
   >
   > 需要注意的是：当使用 git config 命令中的 --global 参数时，你的系统中所有git仓库都将使用这个配置，当然你也可以针对某一个仓库设置姓名和密码

   关于git用户的配置信息，可以通过以下命令来查看

   ~~~shell
   git config user.name              # 查看用户名
   git config user.password          # 查看用户密码
   git config user.email             # 查看用户邮箱
   git config --list                 # 查看配置信息
   ~~~

   如果需要修改这些配置信息，可以使用如下命令

   ~~~shell
   git config --global user.name = "new name"    # 修改用户名，同样的可以修改密码和邮箱
   ~~~

2. ##### 创建一个版本仓库

   在了解版本仓库之前，我们首先来了解下，git的各个区域之间的关系

   ![1090617-20181008211557402-232838726](D:\07-typore文档\1090617-20181008211557402-232838726.png)
   
   ~~~shell
   workspace         # 工作区
   index             # 暂存区（.git文件中有一个index文件，用于管理从工作区提交的修改）
   repository        # 版本仓库
   remote            # 远程仓库
   ~~~
   
   那么我们来创建一个版本仓库
   
   > ~~~shell
   > mkdir fileName         # 创建一个仓库（目录）
   > cd fileName            # 进入这个仓库（目录）
   > git init               # 初始化这个仓库，使其可以被git管理
   > ~~~
   >
   > 执行完 git init 这条命令后，fileName这个git仓库就被建好；你将会看到，这里多了一个.git的目录，这个目录是git 用来跟踪管理代码仓库的，我们称这个.git目录为该项目的版本仓库。
   >
   > 而fileName文件夹，除了.git 目录其他区域为工作区域
   
   假如你需要将工作区的修改提交到版本仓库进行管理，你需要进行以下两步操作
   
   ~~~shell
   # 如果你需要查看工作区与暂存区相比有哪些内容进行了修改
   git status
   # 1.将本地区的修改添加到暂存区
   git add 1.md
   git add 2.md
   # 当然，上面两步git add 可以用一步来完成
   git add .          # 将全部本地区的修改，添加到暂存区
   # 2.将暂存区修改提交到本地仓库
   git commit -m "提交说明"
   ~~~
   
   到此为止，你已经完成了版本仓库的创建，并将修改提交版本仓库仓库

#### 版本管理

1. ##### 管理修改

   查看修改内容 -> 提交开发区修改到暂存区(index) -> 提交暂存区修改到版本仓库

   ~~~shell
   git status              # 查看已修改的文件
   git add                 # 将开发区的修改提交到暂存区
   git commit -m ""        # 将暂存区修改提交到版本仓库
   ~~~
   
   查看工作区和版本仓库的区别
   
   ~~~shell
   git diff HEAD -- a.md               # 查看a.md文件在工作区和版本仓库的区别
   ~~~
   
   
   
2. ##### 版本回退

   现在我们已经知道了如何提交修改到版本库，以后我们将会进行多次修改，多次提交以后，如果需要回退之前的版本，那么我们该怎么做呢？

   > 首先，我们可以通过 git log 查看最近到最远提交到版本仓库的commit
   >
   > ~~~shell
   > $ git log
   > commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
   > Author: Michael Liao <askxuefeng@gmail.com>
   > Date:   Fri May 18 21:06:15 2018 +0800
   > 
   >  append 3
   > 
   > commit e475afc93c209a690c39c13a46716e8fa000c366
   > Author: Michael Liao <askxuefeng@gmail.com>
   > Date:   Fri May 18 21:03:36 2018 +0800
   > 
   >  add 2
   > 
   > commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
   > Author: Michael Liao <askxuefeng@gmail.com>
   > Date:   Fri May 18 20:59:18 2018 +0800
   > 
   >  wrote 1
   > ~~~
   >
   > 我们可以看到，按照时间倒叙排列显示了你的commit，所以在执行git commit -m "提交说明" 说明的重要性，你可以显而易见的看出，之前提交了哪些内容
   >
   > 同时，你还可以看到 log 信息中有许多类似于：eaadf4e385 的16进制编码是commit id（版本号）

   

   下面我们可以开始进行版本回退了

   使用 git reset 命令 ，在这里HEAD^就是返回到上一次修改，HEDA^^会返回上上次修改，当然如果你要返回100次之前的修改使用HEAD~100即可，当然如果想回退到指定的修改，可以直接使用HEAD eaadf4e385（commit id）

   ~~~shell
   $ git reset --hard HEAD^              # 回退到上次修改
   
   $ git reset --hard HEAD^^             # 回退到上上次修改
   
   $ git reset --hard HEAD~100           # 回退到前100次修改
   
   $ git reset --hard eaadf4e385         # 回退到指定的修改
   ~~~

3. ##### 撤销修改

   实际上，当需要撤销修改的情况有两种

   * 工作区修改的撤销，使用 **git checkout --  file**

     * 当工作区的修改未提交到暂存区

       ~~~shell
       # 方法一：手动撤销已有修改，适用于修改较少的情况
       
       # 方法二：使用git checkout -- file
       git checkout -- a.md          # 工作区内容回退至和版本仓库同样的版本
       
       ~~~

     * 当工作区的修改提交到暂存区后,又进行了修改

       ~~~shell
       git checkout -- a.md         # 工作区内容回退至和暂存区同样的版本
       ~~~

       

   * 暂存区修改的撤销，使用**git reset HEAD file**

     ~~~shell
     git reset HEAD file             # 用于撤销未 commit 的修改
     
     git reset HEAD^                 # 用于撤销已 commit 的修改
     ~~~

4. ##### 删除文件

   删除文件有两种情况：

   * 确实需要删除文件

     ~~~shell
     # 方法一
     git rm file
     git commit -m ""
     
     #方法二：先本地删除文件后
     git add 
     git commit -m ""
     ~~~

     

   * 工作区误删文件，需要从版本仓库恢复

     ~~~shell
     git reset HARD commit_id       # （类似从版本仓库回退修改）将修改从版本仓库回退到未提交暂存区状态
     
     git checkout -- file           # 未提交到暂存区的修改回退
     
     
~~~
     


#### 远程仓库

1. ##### 关联远程仓库

   当我们在本地建立了一个仓库后，实现多用户同步管理，可以建立一个远程仓库，以github为例

   > 1. 进入github后找到create a new repo，手动创建一个远程仓库
   >
   > 2. github会自动生成一个仓库的地址
   > 3. 执行：git remote add origin 远程仓库地址，将本地仓库和远程仓库进行连接
   > 4. 执行：git push -u origin master 将本地仓库master分支推送到远程仓库的master分支，同时-u命令还会把两个master分支关联起来

2. ##### 从远程仓库克隆

   如果从零开始开发，最好的方式是先创建远程仓库，然后从远程克隆

   > 1. 首先创建一个新的远程仓库
   > 2. 在建立好远程仓库后，使用：git clone 远程地址， 来克隆一个本地库

#### 分支管理

1. ##### 创建和合并分支

   我们知道：git的每次提交都会被串成一条时间线，截止到目前位置，只有一条时间线，这条线就是master分支，严格来说HEAD是是指向分支的，分支指向每一次提交

   一开始的时候，master每次提交，master分支都会向前移动一步，这样提交的次数越多，这条记录的线就越长

   ![0](D:\07-typore文档\0.png)

   当然，为了协同开发，我们会建立一些别的分支，当建立一个dev分支时，git会创建一个dev的指针，执向当前的master的提交，再把HEAD执行dev，这样就表示当前分支在dev上了

   ![l](D:\07-typore文档\l.png)

   这样，当dev每提交一次，dev分支就会向前增加一个记录，而master指针还指向其最近的那次提交，保持不变

   ![3](D:\07-typore文档\3.png)

   当我们在dev上完成开发后，需要将dev合并到master上，git会直接把master指向dev的当前提交，就完成了合并

   ![4](D:\07-typore文档\4.png)

   这样我们就完成了新分支的创建以及合并分支

   下面我们来总结一下

   ~~~shell
   git branch                    # 查看分支
   
   git branch dev                # 创建dev分支
   
   git switch dev
   git checkout dev              # 切换分支
   
   git switch -c dev
   git checkout -b dev           # 创建并切换分支
   
   git merge dev                 # 合并dev分支到当前分支
   
   git branch -d dev             # 删除分支
   ~~~

2. ##### 解决冲突

   冲突发生原因

   > 当不同分支的开发人员修改了同一个段代码，当合并时会提示冲突，需要手动处理冲突

   

3. ##### 分支策略

   在实际开发中，需要遵循的开发规则

   > master                  # 一般为版本分支
   >
   > dev                        # 为开发分支
   >
   > other                    # 其他分支应该为每个成员的开发分支，每个一段时间去dev分支合并一次

   ![5](D:\07-typore文档\5.png)

   

4. ##### bug分支

   在开发过程中，如果遇到了一个bug需要我们紧急处理，而我们的开发区的工作进行了一半，还不能提交，此时

   ~~~shell
   git stash                # git 提供了git stash，来把当前的工作区修改保存取来，在后面恢复后继续工作
   ~~~

   保存完修改后，切换到版本分支，在版本分支上创建一个bug分支，执行一系列解决bug操作

   ~~~shell
   git checkout master      # 切换到版本分支
   
   git checkout -b bug01    # 创建bug分支，并在该分支进行一系列解决bug的操作
   
   git add                  # 添加修改
   
   git commit -m ""         # 提交bug
   
   git checkout master      # 切换到master分支
   
   git merge bug01          # 合并bug01分支到master分支
   
   # 下面我们继续回到开发区进行开发,并将工作区保存的代码释放出来继续开发
   git checkout dev
   
   git stash list  
   stash@{0}: WIP on dev: f52c633 add merge    # 查看工作区保存的代码
   
   git stash apply          # 恢复工作区的代码
   
   git stash drop           # 删除保存的stash内容
   
   git stash pop            # 恢复工作区代码，并删除保存的stash内容
   
   ~~~

   这里我们思考一个问题，dev分支的代码会合并到master分支，master分支的bug同样会在dev出现，但我们只解决了master分支的bug，dev该怎样处理呢？

   ~~~shell
   git cherry-pick commit_id       # 拉取在master上提交的修改到dev
   ~~~

   