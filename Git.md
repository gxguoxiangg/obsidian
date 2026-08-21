版本控制VCS

工作目录下文件的两种状态：已跟踪或未跟踪

#### 检查当前文件状态

```bash
git status

# 缩短输出
git status [-s | --short]
```

#### 跟踪新文件

```bash
git add [文件名 | 路径]
# 如果是目录，将递归跟踪目录下的所有文件
```

#### 忽略文件

有些文件无需纳入Git管理，也不希望总是出现在未跟踪文件列表中；比如日常文件等等。

在这种情况下，可以创建一个名为`.gitignore`文件，列出要忽略的文件模式

```bash
cat .gitignore
*.[oa]
*~
```

文件`.gitignore`的格式规范如下：

- 所有空行或者以 `#` 开头的行都会被 Git 忽略。
- 可以使用标准的 glob 模式（regex简化版）匹配，它会递归地应用在整个工作区中。
- 匹配模式可以以（`/`）开头防止递归。
- 匹配模式可以以（`/`）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上叹号（`!`）取反。

如：

```bash
# 忽略所有的.a文件
*.a

# 但是跟踪所有的lib.a,即使你在前面忽略了.a文件
!lib.a

# 只忽略当前目录下的TODO文件，而不忽略subdir/TODO
/TODO

# 忽略任何目录下名为build的文件夹
build/

# 忽略
```

在最简单的情况下，一个仓库可能只根目录下有一个 `.gitignore` 文件，它递归地应用到整个仓库中。 然而，子目录下也可以有额外的 `.gitignore` 文件。子目录中的 `.gitignore` 文件中的规则只作用于它所在的目录中。 （Linux 内核的源码库拥有 206 个 `.gitignore` 文件。）多个 `.gitignore` 文件的具体细节超出了本书的范围，更多详情见 `man gitignore` 。

#### 查看已暂存和未暂存的修改

查看未暂存的修改

```bash
git diff
# 此命令比较的是工作目录中当前文件和暂存区域快照之间的差异。
# 也就是修改之后还没有暂存起来的变化内容。
```

查看已暂存的修改

```bash
git diff --staged
# 或
git diff --cached
```



#### 提交更新

已修改但未暂存的文件只会保留在本地磁盘。 所以，每次准备提交前，先用 `git status` 看下，你所需要的文件是不是都已暂存起来了， 然后再运行提交命令 `git commit`：

```bash
git commit

# 你也可以在 commit 命令后添加 -m 选项，将提交信息与命令放在同一行，如下所示：
git commit -m "Story 182: Fix benchmarks for speed"

[master 463dc4f] Story 182: Fix benchmarks for speed
 2 files changed, 2 insertions(+)
 create mode 100644 README
 
 # 可以看到，提交后它会告诉你，当前是在哪个分支（master）提交的，本次提交的完整 SHA-1 校验和是什么（463dc4f），以及在本次提交中，有多少文件修订过，多少行添加和删改过。
```

#### 跳过使用暂存区域

Git 提供了一个跳过使用暂存区域的方式， 只要在提交的时候，给 `git commit` 加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤：

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md

no changes added to commit (use "git add" and/or "git commit -a")
$ git commit -a -m 'added new benchmarks'
[master 83e38c7] added new benchmarks
 1 file changed, 5 insertions(+), 0 deletions(-)
```

这很方便，但是要小心，有时这个选项会将**不需要**的文件添加到提交中。

#### 移除文件

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。 可以用 `git rm` 命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。

如果只是简单地从工作目录中手工删除文件，运行 `git status` 时就会在 “Changes not staged for commit” 部分（也就是 *未暂存清单*）看到：

```bash
$ rm PROJECTS.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    PROJECTS.md

no changes added to commit (use "git add" and/or "git commit -a")
```

然后再运行 `git rm` 记录此次移除文件的操作：

```bash
$ git rm PROJECTS.md
rm 'PROJECTS.md'
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    deleted:    PROJECTS.md
```

下一次提交时，该文件就不再纳入版本管理了。 如果要删除之前修改过或已经放到暂存区的文件，则必须使用强制删除选项 `-f`（译注：即 force 的首字母）。 这是一种安全特性，用于防止误删尚未添加到快照的数据，这样的数据不能被 Git 恢复。

即：

```bash
# 如果在工作区删除了某文件,且该文件已暂存
rm foo

# 会在未暂存清单中看到
git status

# 运行git rm 记录此次移除文件操作
git rm foo

# 提交后，该文件就不再纳入版本管理了
git commit


# 或者是加上-f选项
git rm -f foo
# 这是一种安全特性，用于防止误删尚未添加到快照的数据，这样的数据不能被 Git 恢复
```

以上的删除会一同删除工作区的文件，如果想把文件从暂存区域移除，但仍然希望保留在当前工作目录中，为达到这一目的，使用 `--cached` 选项：

```bash
git rm --cached foo
```



#### 移动文件

要在 Git 中对文件改名，可以这么做：

```bash
git mv file_from file_to
```



#### 查看提交历史

```bash
git log

# 显示每次提交所造成的差异,使用-2限制显示最近的两次提交
git log -p 或 --patch -2

# 显示每次提交的简略统计信息
git log --stat

# 以不同的默认格式显示提交历史
git log --pretty=oneline  or
		--pretty=.....
		
# 以 ASCII 图形显示分支与合并历史。
git log --graph
```



#### 撤销操作

覆盖提交，使用`--amend`

```bash
git commit -m 'initial commit'
git add forgotten_file
git commit --amend
```

取消暂存的文件

```bash
# 取消暂存的文件，但保留对该文件的修改，git status中有对该命令的提示
git reset HEAD -- <unstage_file>


# 取消已暂存的文件，并回退到最后一次提交（HEAD）的状态
git restore --staged <unstage_file>
git restore --staged <ustg_file1> <ustg_file2> ...
git restore --staged .	# 一次性取消所有暂存
```

撤销对文件的修改

```bash
# git status也同样有提示，完全放弃更改
git checkout -- unmodified_file
```

#### 远程仓库

查看远程仓库

```bash
git remote
origin	# 列出每一个远程服务器的简写

git remote -v
origin https://github.com/schacon/ticgit (fetch)
origin https://github.com/schacon/ticgit (push)
# 会显示需要读写远程仓库使用的简写和对应的URL
```

添加远程仓库

```bash
git remote add <shortname> <url>
```

从远程仓库中抓取和拉取

```bash
git fetch <remote>
```

克隆命令的本质

```bash
# 两者是相同的
git clone URL
git remote add origin URL
```

推送到远程仓库

```bash
# 只有当有写入权限时，并且之前没有人推送过，此命令才能生效
git push <remote> <branch>
```

查看某一个远程仓库

```bash
# 如果像要查看某一远程仓库的更多信息
git remote show <remote>
```

远程仓库的重命名和移除

```bash
git remote rename <old_remote> <new_remote>

git remote <remove,rm> <remote>
```



#### 阶段总结

现在，可以完成基本的本地操作：

- 创建或克隆一个仓库

- 进行更改

- 暂存并提交更改

- 浏览仓库更改历史


#### 数据模型

文件被称为blob对象，目标则被称为tree，快照则是被追踪的最顶层的树

历史记录是由一个由快照组成的DAG（有向无环图）



**对象**

Git中的对象可以是blob，tree或commit

Git在存储数据时，虽有对象都会基于它们的SHA-1哈希值进行寻址

```bash
objects = map<string, object>

def store(object):
	id = sha1(object)
	objects[id] = object

def load(id)
	return objects[id]
	
# 即对象存储才用哈希表存储,哈希函数为SHA1
# val = obj, key = SHA1(val)  hash[key] = val
```

Blobs，Tree，Commit都一样，它们都是对象，当它们引用其他对象时，它们并没有实际在硬盘上保存这些对象，而仅仅是保存了它们的**哈希值**做为引用

 可以通过`git cat-file -p 698281bc680d1995c5f4caaf3359721a5a58d48d`来进行可视化	



**引用**

现在，所有的快照都可以通过它们的 SHA-1 哈希值来标记了。但这也太不方便了，谁也记不住一串 40 位的十六进制字符。Git 的解决方法是给这些哈希值赋予人类可读的名字，也就是引用（references）。引用是指向提交的指针。与对象不同的是，它是可变的（引用可以被更新，指向新的提交）。例如，`master` 引用通常会指向主分支的最新一次提交。

这样，Git 就可以使用诸如 “master” 这样人类可读的名称来表示历史记录中某个特定的提交，而不需要在使用一长串十六进制字符了。

有一个细节需要我们注意， 通常情况下，我们会想要知道“我们当前所在位置”，并将其标记下来。这样当我们创建新的快照的时候，我们就可以知道它的相对位置（如何设置它的“父辈”）。在 Git 中，我们当前的位置有一个特殊的索引，它就是 ==**HEAD**==。





#### 创建分支

```bash
git branch branch_name
```

切换分支

```bash
git checkout branch_name
```

创建并切换到新分支

```bash
git checkout -b branch_name
```

删除分支

```bash
git branch -d branch_name
```



查看分支历史

```bash
git log --oneline --decorate --graph --all
# 会输出你的提交历史、各个分支的指向以及项目的分支分叉情况
```

创建新分支只涉及到增加一个新的指针，而不会复制实际的文件内容。git鼓励创建分支，master分支和普通分支没有任何不同



#### 合并分支

```bash
git merge branch_name
```

处理冲突





#### 分支管理

`git branch`命令不只是可以创建和删除分支，如果不加任何参数运行它，会得到当前所有分支的一个列表。

```bash
$ git branch
  iss53
* master
  testing
# 其中*表示该分支是HEAD指针
```



#### 远程分支

变基

```bash
git checkout fix
git rebase master
```

变基使得提交历史更加整洁，经过变基，尽管实际的开发工作是并行的，但它们看上去就像是串行的一样，提交历史是一条直线没有分叉



变基并非完美，使用它需要遵循一条准则：

如果提交存在于你的仓库之外，而别人可能基于这些提交进行开发，那么不要执行变基。

变基还有一些魔法需要后续研究



编辑vs合并

根据实际情况，做出明智的选择。总的原则是，只对尚未推送或分享给别人的**本地修改**执行变基操作清理历史， 从不对已推送至别处的提交执行变基操作，这样，你才能享受到两种方式带来的便利。





#### 基本设置

常用全局设置

```bash
git config --global user.name "xxx"
git config --global user.email "xxx"
git config --global core.editor vim

# 这些命令的实际上是对~/.gitconfig文件的更改
```

别名

```bash
# git checkout <branch> -> git co <branch>
git config --gloabl alias.co checkout

# 甚至可以创建自定义的命令
git config --global alias.unstage 'reset HEAD --'
git unstage fileA
```

