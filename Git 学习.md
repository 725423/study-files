# Git 学习

### git命令

​		设置签名

- git config --global user.name  =     #设置用户签名

- git config --global user.email =      #设置用户邮箱（虚拟邮箱，也可以是自己的邮箱）

  <!--这两个都写在了C:\Users\22906\.gitconfig里面-->

  <!--这里的用户签名的作用是为了区分不同操作者，与登录的账号无关-->

​		初始化本地库

- 进入一个目录，然后初始化：git init                 					#生成.git目录
- 查看本地库状态：git status                                                     #刚开始是空的，也没有需要提交的东西
- 新增文件：再查看状态，会有一个红的
- 添加到暂存区：git add 文件名                                                #添加文件到暂存区
- 删除暂存区的文件：git rm --cached 文件名                        #把暂存区的文件删掉，但是工作区还有
- 将暂存区的文件提交到本地库：git commit -m 版本信息 文件名            #提交到本地库       
- 查看日志信息：git reflog
- 查看详细日志信息：git log          

​		版本穿梭

- 先查看历史版本：git reflog
- 切换版本：git reset --hard 版本号
- 然后在此查看版本信息
- Git 切换版本，底层其实是移动的 HEAD 指针

​		git 分支操作

- **什么是分支**：在版本控制过程中，同时推进多个任务，为每个任务，我们就可以创建每个任务的单独 分支。使用分支意味着程序员可以把自己的工作从开发主线上分离开来，开发自己分支的时 候，不会影响主线分支的运行。对于初学者而言，分支可以简单理解为副本，一个分支就是 一个单独的副本。（分支底层其实也是指针的引用）
- **分支的好处**：1.同时并行推进多个功能开发，提高开发效率                                                                                                            2.各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响。失败 的分支删除重新开始即可
- 分支的操作
  - git branch -v：查看分支
  - git branch 分支名：创建分支
  - git checkout 分支名：切换分支
  - git merge 分支名：合并分支
  - 若产生冲突，则手动合并，然后git add 文件名：添加到暂存区
  - git commit -m 合并名称：注意此时不能带文件名
  - 切换分支的本质就是移动 HEAD 指针


### 上传文件到github

1. git init：初始化仓库

2. git remote add origin https://github.com/725423/study-files.git ：建立远程连接

3. git branch -M main：设置分支

4. git push -u origin main：推送到github、 origin:远程仓库的名称（远程仓库连接可在config中查看）

5. 第4步会报一个错误

   1. ```powershell
      $ git push -u origin main
      fatal: 响应状态代码不指示成功: 406 (Not Acceptable)。
      remote: Support for password authentication was removed on August 13, 2021.
      remote: Please see https://docs.github.com/en/get-started/getting-started-with-git/about-remote-repositories#cloning-with-https-urls for information on currently recommended modes of authentication.
      fatal: Authentication failed for 'https://github.com/725423/study-files.git/'
      
      
      解释：Support for password authentication was removed on August 13, 2021：已于 2021 年 8 月 13 日删除了对密码身份验证的支持
      方法：使用token登录
      ```

6. 参考https://blog.csdn.net/qq_42592823/article/details/123913963

7. 然后执行指令：git remote set-url origin https://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@github.com/725423/study-files.git ：origin：远程仓库名称、 http://token命令@github.com/仓库用户名/仓库名.git

8. git push -u origin main : 将远程仓库内容推送到GitHub，main：分支名


### 更新操作

- git commit : 查看哪些修改得没提交
- git add . :全部提交
- git commit -am '新增文件'：提交新增的
- git remote set-url origin https://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@github.com/725423/study-files.git
- git push -u origin master




