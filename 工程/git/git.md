# git rebase

http://jartto.wang/2018/12/11/git-rebase/

## 破窗效应

> 此理论认为环境中的不良现象如果被放任存在，会诱使人们仿效，甚至变本加厉。以一幢有少许破窗的建筑为例，如果那些窗不被修理好，可能将会有破坏者破坏更多的窗户。最终他们甚至会闯入建筑内，如果发现无人居住，也许就在那里定居或者纵火。一面墙，如果出现一些[涂鸦](https://baike.baidu.com/item/涂鸦/1547)没有被清洗掉，很快的，墙上就布满了[乱七八糟](https://baike.baidu.com/item/乱七八糟/64494)、[不堪入目](https://baike.baidu.com/item/不堪入目/1759841)的东西；一条人行道有些许纸屑，不久后就会有更多垃圾，最终人们会视若理所当然地将垃圾顺手丢弃在地上。这个现象，就是[犯罪心理学](https://baike.baidu.com/item/犯罪心理学/6443)中的破窗效应。

关键词：效仿

## 问题

1.不利于代码 `review`
设想一下，你要做 `code review` ，结果一个很小的功能，提交了 `60` 多次，会不会有一些崩溃？

2.会造成分支污染
你的项目充满了无用的 `commit` 纪录，如果有一天线上出现了紧急问题，你需要回滚代码，却发现海量的 `commit` 需要一条条来看。

遵循项目规范才能提高团队协作效率，而不是随心所欲。



## 合并多次提交

* 这篇不错，https://zhuanlan.zhihu.com/p/462530860

1. 

```shell
# 合并过去4次提交
git rebase -i HEAD~4
```

2. 修改需要合并的信息

   ```tex
   pick b0e6655 Delete .lock 
   s f4e3f09 Delete .log 
   s da66e6a Delete version.ini
   s 25c38c4 remove .class files
   ```

   * 选中第一个，后面三个都合并到第一个中

3. 编辑提交信息

   ```tex
   
   # This is a combination of 4 commits. The first commit's message is:
   
   Delete .lock
   
   # This is the 2nd commit message:
   
   Delete .log 
   
   # This is the 3rd commit message:
   
   Delete version.ini
   
   # This is the 4th commit message:
   
   remove .class files
   
   # Please enter the commit message for your changes. Lines starting
   # with '#' will be ignored, and an empty message aborts the commit.
   #
   # Date:      Sun Jan 3 16:39:23 2021 +0530
   #
   # interactive rebase in progress; onto 652d2fe
   # Last commands done (4 commands done):
   #    pick b0e6655 Delete .lock
   #    s f4e3f09 Delete .log 
   #    s da66e6a Delete version.ini
   #    s 25c38c4 remove .class files
   # No commands remaining.
   # You are currently editing a commit while rebasing branch 'master' on '652d2fe'.
   #
   # Changes to be committed:
   #       new file:   github-git-notes.txt
   #
   ```

   * 可以直接删掉，在第一行添加新的信息



## rebase 删除找回

https://zhuanlan.zhihu.com/p/450129471

## 避免在 pull 的时候产生merge

推荐设置 `git config --global pull.rebase true`

# git diff

- 尚未缓存的改动：**git diff**
- 查看已缓存的改动： **git diff --cached**
- 查看已缓存的与未缓存的所有改动：**git diff HEAD**
- 显示摘要而非整个 diff：**git diff --stat**，==这个非常有用，能看到整个diff新增和删除的个数==

## --name-only

* 只查看修改的文件名

## --name-status

* 查看文件变动状态，只能查看文件名

## --diff-filter

git diff --diff-filter 可以过滤几种不同的变化：

```ini
--diff-filter=[(A|C|D|M|R|T|U|X|B)…[*]]
```

Added (A), Copied (C), Deleted (D), Modified (M), Renamed (R), have their type (i.e. regular file, symlink, submodule, …) changed (T), are Unmerged (U), are Unknown (X), or have had their pairing Broken (B).

只选择那些添加 (A), 赋值 (C), 删除 (D), 修改 (M), 重命名 (R)的文件, 它们的类型(如 普通文件, 符号链接, 子模块, …) 是否改变 (T), 是否未合并 (U), 是未知 (X), 或它们的对崩溃(B). 任何过滤字符的组合（包括none）均可使用。当组合中包括All或none，如果任一文件匹配了其他选项，就选择了所有路径。如果没有文件匹配其他选项，什么都不做。

* 例如：只查看修改的文件

  ```shell
  git diff --name-only --diff-filter=M
  ```

## --cached

-  **git diff --cached** 查看已缓存的改动： 

# 暂存操作

* git stash

https://zhuanlan.zhihu.com/p/373475727

# HEAD

* HEAD 就是指向当前分支提交指针的引用

## HEAD~ 和 HEAD^ 的区别

https://zhuanlan.zhihu.com/p/591777199



# git 合并冲突

## ours vs theirs - 已经合并的怎么解决冲突？

参考：https://nitaym.github.io/ourstheirs/

* 如果是已经合并了出现冲突，想直接采用某一方的提交，可以使用 ours 和 theirs 参数

```bash
# 采用当前分支的合并
$ git checkout --ours codefile.js
# 采用来源的合并
$ git checkout --theirs codefile.js
```

## git 合并策略

参考：https://blog.walterlv.com/post/git-merge-strategy.html#git-%E5%90%88%E5%B9%B6%E7%AD%96%E7%95%A5

指定git合并策略

```bash
$ git merge origin_branch --strategy=策略
```

例如：

```bash
# 简写
$ git merge origin/master -s resolve
# 全拼
$ git merge origin/master --strategy=resolve
```

合并的策略一共有：

- resolve

  自动合并，算法可能有问题

- recursive （常用）

  默认策略

  当指定为此策略时，可以额外指定下面的这些参数，方法是：

  ```
  1 $ git merge 要合并进来的分支名 --strategy=合并策略 -X diff-algorithm=参数 
  ```

  默认延伸出来的合并策略(如上的参数)：

  * Ours 如果不冲突，那么与默认合并一直；如果发生冲突将采用来自自己一方的修改

  * theirs 这与 ours 相反。如果不冲突，那么与默认的合并方式相同。但如果发生冲突，将自动应用来自其他人的修改

  * patience

    此策略的名称叫“耐心”，因为 git 将话费更多的时间来进行合并一些看起来不怎么重要的行，合并的结果也更加准确。当然，使用的算法是 recursive 即递归三路合并算法。

- octopus

- ours

  > 在合并的时候，无论有多少个合并分支，当前分支就直接是最终的合并结果。无论其他人有多少修改，在此次合并之后，都将不存在（当然历史里面还有）。

- subtree





# 啥是 PR 

* https://www.zhihu.com/question/21682976

## 修改开源仓库流程

1. fork 该仓库
2. git clone 到本地进行修改
3. 提交到自己fork仓库，然后发起 PR，让原作者看到该修改
4. 原仓库 review 这个bug，如果正确就进行合并

![img](https://picx.zhimg.com/80/3e1ac7f58774a79a5dd6bf8e852e4199_720w.webp?source=1940ef5c)