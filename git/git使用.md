---
title: GIT
date: 2016-05-01
---

# git

## 操作计算机的方式
* 1、图形用户界面
* 2、命令行
   * winows  dos
   * linux(unix mac)  shell
   * dos和shell都有两层含义：解析器  编程脚本
## 常用的shell命令 
* **命令的格式：`命令 [可选选项][参数]`**
* 1、pwd(print working directory) : 打印当前目录
* 2、mkdir(make directory) : 创建目录
   * mkdir www
* 3、ls(list) : 显示目录
* 4、ll : 按行显示目录
   * ll -a : 可以显示隐藏文件 
   * 相当于ls -l
* 5、 cd(change dirctory) : 切换目录
* 6、 cat : 查看文件内容
* 7、less : 查看文件，整屏显示，按q退出
* 8、tail : 监控文件
   * tail -f a.txt 监控文件的实时变化
* 9、echo : 向文件写入内容
   * echo 123 > a.txt : 覆盖文件内容
   * echo 456 >> a.txt : 向文件中追加内容 
* 10、touch : 创建文件
   * touch a.txt
* 11、 rm : 删除文件
* 12、rmdir : 删除文件夹，只能删除空文件夹
* 13、mv : 移动文件
   * mv ./www/a.txt ./  将a.txt移动到当前路径 
* 14、cp : 复制文件
* 15、| : 管道符 ，将上一个命令的输出作为下一个命令的输入
* 16、grep : 匹配内容，一般结合管道符使用
   * ls | grep a  : 将ls输出的文件作为输入 用来匹配带a的文件或文件夹 
## VI 编辑器
* 三种模式：命令模式，输入模式，底行模式
* 每种模式都有各自的命令
  *  用户输入 vi +文件，进入命令行模式，此时不允许输入。命令模式下输入ZZ可直接保存退出
  *  按下a或者i，进入输入模式。
  *  按下ESC退出输入模式，进入命令行模式
  *  按下shift+ : 进入底行模式
  *  输入wq，保存并退出vi
* 关于vi编辑器的补充
  * 快速定位到行首： 命令模式  两次g；底行模式 输入0；
  * 快速定位到行尾：底行模式  输入$
* 使用vi搜索内容：先输入左斜杠，然后输入关键字  回车
* 输入vi + 不存在的文件，会打开一个新文件，在保存后会创建一个新的文件
## SSH
* 一种网络协议，用来实现两台计算机之间的通信（具体就是实现远程加密登录）
* Telnet  是一种不加密的通信协议
* 在本地创建公钥和私钥命令：Keygen -t rsd(一种非对称加密的算法)
### 两种加密方式
* 1、对称加密（加密和解密的方式相同）
* 2、非对称加密（公钥加密，私钥解密；私钥加密，公钥解密）
  * 公钥
  * 私钥
### 两种登录方式
* **使用密码登录**
  * 1、客户端发送请求
  * 2、服务器接收到请求之后把服务器的公钥发送给客户端
  * 3、客户端收到公钥后会存储在指定目录（c:\users\用户名\.ssh）
  * 4、客户端再次发送登录请求（密码使用公钥加密）
  * 5、服务器接收到密文后，使用私钥进行解密验证是否正确
* **使用公钥和私钥登录**
  * 1、客户端首先生成自己的公钥和私钥（keygen -t rsa）
  * 2、把客户端的公钥传递给服务器，服务器使用该公钥给客户端授权
  * 3、客户端直接可以免密码登录
## GIT基础
* **git 工作流程**
  * 1、下载代码，在下载的代码基础之上做开发，然后提交
  * 2、新建项目，git init生成一个仓库，在仓库中添加代码，推送到服务器的空仓库中
### git中三个重要的区域
* 1、工作目录
* 2、暂存区
* 3、本地仓库
### git文件的四种状态
* 1、未跟踪(untracked)：工作目录刚创建的文件
* 2、已暂存(staged)：从工作目录提交到暂存区的文件
* 3、已提交(commited)：从暂存区提交到本地仓库的文件
* 4、 已修改(modified)：暂存区修改过的文件
### git命令的使用
* git init  ：初始化一个本地仓库，设置在客户端
* git init --bare ：初始化一个共享仓库，设置在服务器上
* git config --global user.name "名字" ： 设置用户名
* git config --global user.email 邮箱  ：设置邮箱
* git status ：查看文件状态
* git add 文件(多个文件之间以空格隔开) ：将工作目录的文件提交至暂存区  **未跟踪-->已暂存**
* git rm --cached 文件 ： 重新把暂存区中的文件放回工作目录   **已暂存-->未跟踪**
* git add -A /`git add  *` ：表示全部提交  **已暂存-->已提交**
* git commit  -m  ‘描述’ ：把暂存区中的内容**提交到本地仓库**
* git log ： 查看提交的本地仓库记录
* git checkout  -- 文件 ： 取消暂存区文件的修改内容。git checkout .  :  取消全部修改内容
* git reset --hard commitID ：用本地仓库中的快照覆盖工作目录和暂存区中的内容，并且放弃commitID之后的提交
* git reset HEAD  :  使用本地仓库的最新版本覆盖暂存区，但是不覆盖工作目录
* git reset --hard HEAD   : 工作目录和暂存区和本地仓库都会变化
* git reset--soft  HEAD : 只会回滚本地仓库，工作目录和暂存区不会改变
* git reset --mixed HEAD :  默认。用本地仓库的版本覆盖暂存区，也会回滚本地仓库
### 分支操作
* git branch ： 查看分支
* git branch -r  ： 查看远程分支
* git branch -a  ： 查看所有分支
* git branch 分支名称 ：创建分支
* git checkout 分支名称 ：切换分支
* git checkout -b 分支名称 ：创建并且切换分支
* git branch -d 分支名称 ：删除分支
* git merge  被合并的分支名称 ：合并分支
* git push origin --delete  分支名称：删除远程分支
* git push origin+ 空格 +:分支名称：删除远程分支
### 远程命令
* 将代码推送至远程仓库
     * git push 远程仓库的地址  本地分支：远程分支(可省，默认为本地分支名称)
     * git push 远程仓库的地址  本地分支
* 从远程仓库拉代码
     * git pull 远程仓库的地址  远程分支：本地分支（可省，默认为远程分支名称）
     * git pull 远程仓库的地址  远程分支
* git clone 远程仓库的地址  文件名称（可选，如果缺省，则使用远程仓库的名称）
     * 会自动在远程分支和本地分支之间建立一个关联
     * git clone 的项目，会自动建立名为origin的远程仓库地址的别名
     * 可以使用git push origin 本地分支 可以直接推送至远程仓库
     * git clone 会自动跟踪远程分支（可以直接使用git pull进行代码更新）
     * 如果git pull 拉下来的代码希望建立跟踪分支，需要执行git branch --set-upstream-to=origin/master master 
* git remote add origin(别名) root@IP地址：创建一个远程仓库的别名
* git remote  ： 查看远程主机的别名
* git remote show origin　 ： 查看远程仓库别名的具体地址
* git fetch ： 获取远程代码，但不会合并；可以使用git merge  origin/分支   命令进行代码合并
* git stash : 保存当前的版本状态，即没有做完的工作，还没有执行commit
* git stash pop : 恢复保存的内容，继续进行
