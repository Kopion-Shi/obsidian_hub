# git学习

## 一、初始化项目文件

- 进入需要管理的项目文件夹

- 执行初始化命令

  ```python
  git init
  ```

- 管理目录下的文件状态

```
git status
ps: 新增文件和修改过的文件都是红色的
```

- 管理指定文件

  ```
  git add 文件名
  git add .
  ps:文件由红变绿
  ```

- 个人信息配置：用户名 邮箱【一次配置即可】

  ```
  git config --global user.enail "you@example.com"
  git config --global user.name "Your name"
  ```

- 生成版本

  ```
  git commit -m "描述信息"
  ```

- 查看版本记录

```
git log
```

- 回滚

  ```
  回滚至之前版本
  git log
  git reset --hard 版本号
  
  回滚至之后的版本
  git reflog
  git reset --hard 版本号
  ```

- 小结

  ![image-20230207214725468](image-20230207214725468.png)菜单



## 二、分支

象：分支可以提供多个环境，可以把工作从开发主线上分离下来，以免影响主线

数：

![image-20230207215455051](image-20230207215455051-16757780975131.png)

### 1、命令总结

```
查看分支：git branch
创建分支：git branch 分支名
切换分支：git checkout 分支名
分支合并：git merge 要合并的分支 （可能产生冲突，ps：切换分支以后再合并）
删除分支：git branch -d 分支名称
```

工作流

![image-20230207215912916](image-20230207215912916.png)

### 2、提交远程仓库

![image-20230207220109094](image-20230207220109094.png)

- 在github注册（自行百度）（在家）

- 将本地代码推送到远程代码仓（第一次创建远程项目）：

  https://github.com/Kopion-Shi/python_note

  ```
  #给远程代码仓起别名：origin
  git remote add origin https://github.com/Kopion-Shi/python_note
  
  #将本地master分支内容及版本信息推送到远程仓库
  git push origin master
  
  #将本地dev分支内容以及版本信息推送到GitHub
  git push origin dev 
  ```

- 从远程代码仓拉取代码（在公司）

```
git clone https://github.com/Kopion-Shi/python_note

#查看分支：默认获取到得只有master分支
git Branch 

# 创建dev分支且和远程dev分支同步
git branch dev origin/dev

# 切换到dev分支
git checkout dev

# 继续开发新功能。对文件内容一通操作

#添加文件到版本库的暂存状态
git add .  

# 提交新功能到版本库的分支
git commit -m '新增功能1'

# 提交dev分支内容到远程GitHub托管仓库的dev分支
git push origin dev
```

（回家，继续开发）

```
# 切换到dev分支
git checkout dev

# 从远程GitHub仓库获取dev分支最新内容，并合并到本地
git pull origin dev 

#继续开发新功能
-----

# 添加文件到版本库的暂存状态
git add .


# 提交新功能到版本库的分支
git commit -m '家里开发功能1'
```

（昨天晚上的代码未提交到github，去公司接着搞）

```
# 切换到dev分支
git checkout dev  

#从GitHub仓库获取dev分支最新内容到版本库的分支
git fetch origin dev

# 将版本库的分支内容合并到工作
git merge origin/dev  

# 将版本库的分支内容合并到工作区
git merge origin/dev 

# 继续开发新功能

# 添加文件到版本库的暂存状态
git add .

# 提交新功能到版本库的分支
git commit -m 'xxxxxxxxxxx'  
```

## 三、协同开发

协同开发一般有两种方式：****

- 合作者：将其他用户添加到仓库合作者中之后，该用户就具有向当前仓库提交代码
- 组织：创建一个组织，然后再该组织下可以创建多个项目，组内成员可以向组内所有项目提交代码。PS：也可以对某个项目指定合作者

### 3.1**协同开发整个过程**

- 创建程序

- - 用户A创建程序，提交到GitHub
  - 用户B克隆项目
  - 用户C克隆项目

- 开发功能

- - 用户A开发功能1
  - 用户B开发功能2
  - 用户C开发功能3

- 提交

- - 用户A提交功能1，并push（A用户手速快，先提交。）
  - 用户B提交功能2，无法push，因为GitHub上已经有其他人提交的新代码。
    解决方法：从GitHub上获取最新代码并合并到本地，提交自己开发的功能2。
  - 用户C提交功能3，无法push，无法提交，因为GitHub上已经有其他人提交的新代码。
    解决方法：从GitHub上获取最新代码并合并到本地，提交自己开发的功能3。

- 获取最新代码

  - 用户A获取最新代码
  - 用户B获取最新代码
  - 用户C获取最新代

后续持续更新中