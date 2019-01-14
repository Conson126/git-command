# git知识分享
---------

##版本控制介绍 

###一、为什么要用版本控制

1.备份多个版本，费空间，费时间

2.难于恢复至以前正确版本

3.容易引发BUG

4.解决代码冲突困难

5.代码管理混乱

6.难于追溯问题代码的修改人和修改时间

7.项目版本发布困难
 
###二、什么是版本控制

版本控制(Revision control)是维护工程蓝图的标准做法，能追踪工程蓝图从诞生一直到定案的过程。是一种记录若干文件内容变化，以便将来查阅特定版本修订情况的系统。 

##git介绍
###一、GIT简介 

1、Linus的第二个伟大作品。2005年由于BitKeeper软件公司对Linux社区停止了免费使用权。Linus迫不得己自己开发了一个分布式版本控制工具，从而Git诞生了。 

2、目前使用Git作为版本控制的开源软件：Linux kernel，Android, jQuery, Ruby on Rails，Debian…

3、Eclipse上使用Git的项目数量也已经超过了使用SVN的仓库数。

###二、git工作模型

1、中心版本库（origin）

![](http://roclinux.cn/wp-content/uploads/2010/12/centr-decentr.png)

2、分支

* 主分支（master）可发布状态代码

* 开发分支（develop）最新开发进度代码

![](http://roclinux.cn/wp-content/uploads/2010/12/git-master-develop.png)

3、团队协作开发

* 辅助分支：  Feature branches、 Release branches、 Hotfix branches

![](http://roclinux.cn/wp-content/uploads/2010/12/bigpicture-git-branch-all.png)

###三、在服务器上搭建一个自己的git仓库
####1、免费托管项目代码的仓库：github、gitlib、bitbucket ...

####2、搭建一个git远程仓库

* 你需要一个服务器环境----（linux，centos...）
	* [https://kiwivm.64clouds.com/](https://kiwivm.64clouds.com/ "VPS服务器")     
	* 23.105.220.74
	* psw ： UrZFmFf3o3eg
	* ssh port ： 27276

* 安装git客户端(windows，mac...略过)

	* yum install git    (版本太旧 1.7.1)
	* 安装最新版教程：
	* yum groupinstall "Development Tools"
	* yum install zlib-devel perl-ExtUtils-MakeMaker asciidoc xmlto openssl-devel
	* yum install perl-ExtUtils-CBuilder perl-ExtUtils-MakeMaker
	* cd ~
	* wget -O git.zip https://github.com/git/git/archive/master.zip
	* unzip git.zip
	* cd git-master
	* autoconf
	* ./configure
	* make && make install
	* ln -s /usr/local/bin/git /usr/bin/    （如果报错，删除旧链接：rm -rf /usr/bin/git）
	* git --version

###3、创建：
* 方式一：直接在远程服务器下创建，然后clone
	* mkdir demo.git
	* ce demo.git
	* git --bare init  （使用此命令进行远程仓库初始化）
* 方式二：在本地创建，添加到远程（前提：必须有一个远程空仓库   适用于将已有工程直接推送到远程仓库）
	* 找到你的工程目录
	* cd工程根目录，git init  ——>变成了一个本地仓库
	* git status会看到很多git未提交文件，此时可以git add，git commit
	* 设置远程仓库地址：git remote add origin ssh://root@23.105.220.74:28700/rep/project.git
		<origin其实就是一个昵称，代表的是ssh://root@23.105.220.74:28700/rep/project.git这个url，可以添加多个远程仓库的url。昵称可以是任何其他的名字>
	* 将本地代码推送到该git远程仓库：git push origin master:master
	* 添加之后，只是配置了本地仓库的远程仓库信息  git remote show origin

* 添加忽略文件：[https://github.com/git/git](https://github.com/git/git "GIT CLIENT") 
 	* 忽略开发工具自动生成的文件
 	* 以clone下来的工程中是不是包含改忽略的文件或文件夹为目标
* 添加忽略文件的两种方式
* 1、第一次初始化时，没有add任何文件的情况的时候添加忽略文件
	* 创建一个gitignore文件：touch .gitignore    vim .gitignore
	* 添加忽略文件： *.iml     build/   a.txt   .idea/     /build/
	* 添加完忽略文件之后，add commit push
* 2、工程已经push到远程，并且存在多次commit历史，但是第一次初始化没有添加忽略文件或者添加的忽略文件不完整
	* 创建gitognore文件
	* 删除忽略文件的git缓存记录： git rm --cache fileName
	* 缓存比较多，使用：git rm -r --cache directory   递归删除
	* 提交忽略文件和删除缓存历史
	
最后检测一下是否添加忽略文件成功

###4、使用SSH协议认证
* ssh协议简介：[http://blog.csdn.net/macrossdzh/article/details/5691924](http://blog.csdn.net/macrossdzh/article/details/5691924)
* 全局配置git用户名和密码账户信息
	* git config --global user.name "用户名"
	* git config --global user.email "邮箱"
* 生成ssh密钥
	* ssh-keygen -t rsa -C "邮箱"
	* 生成过程中，可自定义目录，需要在当前用户下
* 如何通过SSH协议与远程的VPS服务器建立一个链接（以VPS服务器为例）
	* 1、需要进入linux下的etc/ssh/sshd_config文件，使用vim
	* 打开注释部分：
	   	* RSAAuthentication yes
		* PubkeyAuthentication yes
		* AuthorizedKeysFile      .ssh/authorized_keys
	* cd ~
	* cd .ssh (没有的话，创建一个 mkdir .ssh)  修改权限chmod 700 .ssh
	* cd .ssh  ————>  创建一个authorized_keys文件，并修改权限chmod 700 authorized_keys
	* vim authorized_keys  ————> 添加PC端公钥 ————>认证建立成功
	* 2、验证：直接ssh访问VPS服务器
	* 3、可以建立其他用户，添加多个其它用户的公钥到该ssh服务器，可以直接对该VPS上的服务器进行读写操作

####5、本地仓库常用操作

* 1、配置：config
	* 必须配置用户名和密码
	* git config --global user.name ""
	* git config --global user.email ""
	* git config -l /————>查看配置信息

* 2、克隆
	* cd到某一目录 ——> git clone 仓库名
	* 直接任意目录 git clone 仓库名 目录名    ——>可用于对本地仓库重命名
* 3、查看分支
	* git branch  查看本地分支
	* git branch -a 查看所有分支
	* git branch -r 只查看远程分支
	* git branch -m [oldName] [newName] 分支重命名

* 4、检出
	* git checkout 分支名  切换到某分支
	* git checkout commitID 切换到某次提交上
		* cat .git/HEAD 查看当前分支的HEAD
		* cat .git/refs/heads/分支    查看当前分支指向的commitID

* 5、创建分支并切换到当前分支
	* git checkout -b develop master  基于本地master分支创建一个新分支develop
	* git checkout -b develop origin/master  创建develop分支追踪到远程master分支
* 6、git工作区和暂存区
	* 工作区：文件内容改变后，没有做任何git操作，则修改的文件放在了工作区
	* 暂存区：执行git add 文件名  之后，该文件被放在了暂存区
	* git commit之后，则将暂存区的文件提交到了版本库
	* git cimmit --amend    修改最近一次的commit的message
	* 将工作区文件提交到暂存区：
		* git add 文件名
		* git add .     /————>添加工作区的所有变化内容提交到暂存区
		* git add --all    /————>同上
	* 将暂存区的内容放到工作区
		* git reset HEAD 文件名    /————>对单个文件的操作
		* git reset HEAD . ————>将暂存区的所有文件恢复到工作区
	* 将工作区的内容丢弃掉<该操作仅能丢弃修改，增加的文件不能丢弃>
		* git checkout -- 文件名 / ————> 将某一次修改丢弃
		* git checkout . / ————> 将工作区的所有内容丢弃
		* git stash /暂存 ————> git stash pop /清空顶部暂存内容
	* 将工作区增加的内容丢弃
		* git clean -d
		* git clean -df  / ————>强制丢弃
		
* 7、保存工作现场：git stash
	* 以队列形式保存当前工作现场（工作现场：工作区和暂存区）
	* 最简单：git stash
	* git stash save "message"	/————>自定义一个暂存信息
	* 恢复：
		* git stash list
		* git stash apply stash@{id}  /————>恢复但不删除暂存内容
		* git stash pop  /————>恢复栈顶并删除该暂存内容
			* 默认重置暂存区内容
		* 不重置暂存区，恢复保存的工作现场
		* git stash pop --index stash@{id}
		* git stash apply --index stash@{id}
	* 恢复到旧分支：
		* git stash branch 分支 stash@{id}
	* 删除：
		* git stash drop stash@{id}
		* git stash clear /————>删除暂存区所有暂存内容
	* 查看：
		* git stash show stash@{id}
		* git show 变化文件
* 8、查看：
    git show
	* git show &id /————> 查看某次修改记录
	* git log /————>查看所有提交历史
	* git log $id /————>查看某次提交历史信息
	* git log 文件名 /————>查看某个文件每次提交历史纪录
	* git log -p 文件名 /————>查看某个文件修改的所有详细内容
	* git log -p -3 /————>查看最近3次修改的详细内容
	* git log --pretty=oneline  /————>显示所有提交历史修改的内容
	* git log --pretty=oneline 文件名  /————>查看某个文件修改的历史纪录
	* git blame fileName /————>以列表形式查看某文件修改历史
    	* git whatchanged 文件名 /————>显示某个文件的所有提交历史
	* git blame 文件名    /————>显示文件每行内容是谁修改的
    
    git diff
    * git diff 比较的是工作区和暂存区的差别
    * git diff --cached 比较的是暂存区和版本库的差别
    * git diff HEAD 可以查看工作区和版本库的差别
* 9、拉去与获取：git fetch & pull
	* 查看git fetch和git pull默认从远程哪个分支
		* git remote -v
		* git fetch
		* git pull
	* 指定分支获取和拉取
		* git fetch remoteUrl
		* git pull remoteUrl branch
* 10、推送：git push
	* git push origin 本地分支（当前分支）：远程分支名
		* 远程分支不存在则新建，存在则同步代码
	* git push origin ：远程分支名   /————>删除远程分支
	* git push origin --delete 远程分支 
* 11、删除操作
	* git rm fileName /————>移除、删除文件
	* git rm --cached fileName  /————>停止跟踪某文件
	* git branch -d 分支 /————>删除某分支
	* git branch -D 分支 /————>删除分支（强制，-d删除不了的时候）
	* git tag -d [tagName] /————>删除某个标签<删除本地的>
	* git push origin --delete tag [tagName] /————>删除远程tag
	* git push origin :/refs/tags/[tagName] /————>删除远程tag
* 12、打标签：git tag
	* 用处：常用与版本发布：[https://github.com/shadowsocks/shadowsocks-android/releases](https://github.com/shadowsocks/shadowsocks-android/releases)
	* git tag /————>查看当前仓库所有tag
	* git tag {v1.0.1-beta} /————>创建一个标签
	* git checkout [tagName] /————>切换到某个标签
	* git show [tagName] /————>查看改版本详细信息
	* git push origin [tagName] /————>推送某个标签到远程
	* git push origin -tags /————>推送本地所有标签到远程仓库
* 13、其他
* 14、查看文件每一行的是谁修改的
	* git blame 文件名 
* 15、设置本地某个分支追踪到远程分支：
	* git branch --set-upstream-to=origin/<branch> master  /————>设置本地的master分支追踪到origin/<branch>	


	



	


