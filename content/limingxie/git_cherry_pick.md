---
title: "git命令 cherry-pick 的使用简介"
date: 2018-07-30T07:28:49+08:00
draft: true
---
上一篇文章介绍了merge和rebase的用法以及原理。   
[git命令merge和rebase的简单应用](https://limingxie.github.io/limingxie/git_merge_rebase/)

这里简单的介绍一下 cherry-pick 的用法。  
什么时候会用到 cherry-pick 命令？  

代码分几个大分支，各自有不同的功能在开发，这时候我们就有这样的需求。  
`只合并某个分支上的某次修改commit到指定的分支上`。  
这时候就可以使用`cherry-pick命令`来操作。

```
$ git cherry-pick commit_id     //想合并的commit_id
```

[图片备用地址](https://limingxie.github.io/images/git_cherry_pick/git1.png)  
![1](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/git_cherry_pick/git1.png)

如果是图1这样使用cherry-pick命令的话，  
会有 a1, a2, a3 的合并项，在代码中需要删除 a1, a2 再次提交解决冲突才行。

喜欢找茬的人会这么问：这么一来和merge有什么区别吗？  
merge的话也不是把 a1, a2, a3 的代码合并过来吗？  
merge合并后把 a1, a2 删掉的话跟cherry-pick有什么区别？

`区别在于日志`。

```
$ git cherry-pick fd4e09c(a3的 commit_id)
error: could not apply fd4e09c... add a3
hint: after resolving the conflicts, mark the corrected paths
hint: with 'git add <paths>' or 'git rm <paths>'
hint: and commit the result with 'git commit'

//...省略掉中间处理冲突的事项

$ git log --oneline --graph

* e08bab6 (HEAD -> dev) cherry-pick a3
* 2da150a add b4
* 5ea1f15 add b3
* 42e92e8 add b2
* 87050eb add b1
* e8f0eb9 first commit
```

```
$ git merge master
Auto-merging 11.txt
CONFLICT (content): Merge conflict in 11.txt
Automatic merge failed; fix conflicts and then commit the result.

//...省略掉中间处理冲突的事项

$ git log --oneline --graph

*   d037c87 (HEAD -> dev) merge master
|\
| * 4ffb788 (master1, master) add a4
| * fd4e09c add a3
| * d621ee2 add a2
| * 4aa0687 add a1
* | 2da150a add b4
* | 5ea1f15 add b3
* | 42e92e8 add b2
* | 87050eb add b1
|/
* e8f0eb9 first commit
```

虽然代码一样，但是留的日子就完全不一样了。  
如果这时候我们再想把 a1, a2 合并过来呢？  
下面以merge的方式把a3的代码合并过来的人就痛苦了。  
因为git以为已经把 a1, a2 合并过来的了。再次合并不会把 a1, a2 合并过来的。  
(`cherry-pick a3后，如果再次merge master分支的话，`  
`两边 master分支 和 dev分支 都会有a3的代码，需要删除一个。`)

喜欢找事的人又开始问： 我把 a3 合并后，把 a2 再合并过来不会有问题吗？  
`这时候建议你先把a3删掉(使用rebase)，再cherry-pick a2, a3。`

[图片备用地址](https://limingxie.github.io/images/git_cherry_pick/git2.png)  
![2](https://mingxie-blog.oss-cn-beijing.aliyuncs.com/image/git_cherry_pick/git2.png)


NND，喜欢找事的人又开始问： b5的开发是基于 a3 怎么办？  
这时候就考验你的git命令的熟悉度了。  
你需要先备份b5的代码，再用rebase去掉a3的代码，再cherry-pick a2, a3  
然后从备份b5代码的分支那里cherry-pick b5的代码....  
自己搞去吧...^^  方法有的是~~  
前提是你得`弄清楚每个命令的原理和使用方法`。

怎么样？ 现在是不是觉得能用git命令，管理和修改你的代码是很简单的事情?  
但是别太骄傲了~ 知道了这些内容才算git的入门。  
我们还是菜鸟，需要学的还很多...^^

----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`