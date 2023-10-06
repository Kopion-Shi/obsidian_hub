# git ignore

规则很简单，不做过多解释，但是有时候在项目开发过程中，突然心血来潮想把某些目录或文件加入忽略规则，按照上述方法定义后发现并未生效，原因是.gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。那么解决方法就是先把本地缓存删除（改变成未track状态），然后再提交：

当我们需要删除`暂存区`或`分支`上的文件, 同时工作区也不需要这个文件了, 可以使用（没啥用）

```python
1 git rm file_path
2 git commit -m 'delete somefile'
3 git push
```

当我们需要删除`暂存区`或`分支`上的文件, 但本地又需要使用, 只是不希望这个文件被版本控制, 可以使用（有用）

核心原理：也是提交了一个删除该文件的记录，但是本地文件不受影响，但是如果提交到了远程上远程分支就会删除这个文件，其他分支pull了之后都会跟着删除。

```pyhton
git rm --cached file_path
git commit -m 'delete remote somefile'
git push
```

所以如果你的需求是某个文件互不影响，那么就要参考以下的做法了，非常危险以及麻烦。

## 比如你的需求是（从这开始看）



1 本地dev有个wsgi.py

2 本地master有个wsgi.py

3 git远程仓库dev有个wsgi.py

4 git远程仓库master有个wsgi.py

5 线上远程master有个wsgi.py

6 如果想让彼此的wsgi.py互不影响，但是此时wsgi.py已经被git追踪了，所以再设置.ignore已经无效了。

这个时候，首先你要保证每个仓库都版本一致。然后从你线上的分支开始

### 一 线上远程master

1 git rm --cached Yanapi/wsgi.py

2 vim .gitignore 添加上wsgi行

3 git add .

4 git commit -m '线上清除wsgi缓存'

5 git pull origin master #ps：这里可能要解决下冲突

6 git push origin master

### 二 本地master

1 git rm --cached Yanapi/wsgi.py

2 vim .gitignore 添加上wsgi行

3 git add .

4 git commit -m '本地清除wsgi git追踪'

5 git pull origin master #ps：这里可能要解决下冲突

7 git push origin master

### 三 本地dev

1 git rm --cached Yanapi/wsgi.py

2 vim .gitignore 添加上wsgi行

3 git add .

4 git commit -m '本地清除wsgi git追踪'

5 git checkout dev

5 git merge master #ps：这里可能要解决下冲突

7 git push origin dev

### 四 本地master

git checkout master

git merge dev #ps:这里可能要解决下冲突

git pull origin master

git push origin master

### 五 远程master

git pull origin master

### 核心原理：

**大前提：**git如果已经追踪了wsgi.py所以.gitignore已经无效了

**上面这么做的核心原理就是：**

1 我的a库清除wsgi.py缓存记录git会提交一个delete wsgi的操作，a库本地wsgi.py保存住了，a库ignore wsgi.py也会生效。

2 b库想合并a库，这个时候b库先把自己wsgi.py的缓存清理掉，并且.ignore忽略wsgi.py然后提交版本，这个时候再合并a库，a库的delete那条记录操作一瞅你的版本记录里面已经有删除wsgi.py了，那么b库本地的wsgi.py就保存住了。

c库合并b库的原理也一样。d库合并c仓库的原理也一样，那么所有库本地的wsgi都保住了，并且胡不影响。