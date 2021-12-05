---
title: Github笔记
date: 2021-12-04 17:27:28
tags:
categories: "学习笔记"
---
记录Github操作的笔记,来源于《Github入门与实践》。
<!--more--> 
Tips：国内经常打不开Github，请在cmd上输入ipconfig /flushdns刷新DNS即可。

# 需要掌握的语法
1. Talklist

2. GFM

# Github基本操作
1. git init 初始化仓库( 本地创建一个文件夹，进去后然后执行本命令)。

2. git status 查看仓库状态。

3. git add 暂存区添加文件。

4. git commit保存仓库的历史记录，是将本地修改过的文件提交到本地库中。git push是将本地库中的最新信息发送给远程库。

5. git log查看提交日志。 git log --graph图表形式查看分支。

6. git diff 查看更改前后的差别。

7. 不妨养成这样的好习惯，在执行 git commit命令之前先执行 git diff HEAD 命令，查看本次提交和上次提交之前有什么区别，但是如果直接执行git diff，由于工作树和暂存区的状态并没有差别，所以不会显示东西。

8. git branch 显示分支，* 代表当前分支 ，添加参数-a可以查看本地仓库和远程仓库的分支信息。

9. git checkout -b  NAME 创建NAME这个分支，并且会马上切换到这个分支。

10.  git checkout master 切换到master这个分支。

11. git checkout - 切回到上一个分支，或者直接像上面一样输入名字。

12. git merge  --no-ff feature-A ，切到master上执行这个语句，并且加上 --no - ff这个参数，就可以让feature-A合并。

13. git reset --hard 哈希值，填写相应的哈希值，就可以回溯版本，git log 只能查看以当前状态为终点的历史日志。所以用git reflog 可以查看当前仓库执行过的操作日志，然后左边就是哈希值，填写命令，就可以回溯到这个历史之前的状态，就像时间穿梭，但是，需要你没有进行Git的垃圾回收功能，才可以用这个reflog功能，其实测试了下这个日志，也就是包含最近的操作过程，即便是前几天操作的。

14. 合并的时候有冲突，当解决好冲突再合并，系统提交的信息是Fix conflict。而我们其实是想记录合并的信息，所以用git commit --amend去修改上次提交的信息（我的理解是相当于修改提交这次上传的备注）。

15. git commit -am "备注信息"这个命令只能提交已经追踪过且修改了的文件(也就是早就存在的文件，只是我们需要修改一点东西)，去过是新增文件就必须使用git commit -m "备注信息"的命令。直接git commit会调用vim去描述更多的信息。

16. 错字漏字等失误叫typo，所以修改备注的时候填这个，搞国际化一点，与时俱进。

17. git rebase -i HEAD~2,当我们只是修改错别字时候，在历史记录里我们想把修改后的和前一次合并起来为一次完美的提交，就需要用这个，输入命令后出来编辑器，需要把第二个左边的pick改成fixup，保存后就可以了。

18. git remote add origin git@github.com:本地目录名字/仓库名字.git    就把本地添加到这个远程仓库。并把远程仓库名称设置为origin（标识符），这里有个知识，origin在git中是什么意思，百度有很详细的解答。

19. 在执行完上面添加仓库，执行git push -u origin master 可以推送到远程仓库。-u参数可以在推送的同时，将origin仓库的master分支设置为本地仓库当前分支的upstream，添加了这个参数，以后直接git pull从远程仓库获取内容时候，本地仓库这个分支就可以直接从origin的master分支获取内容，省去了另外添加参数的麻烦。

20. 推送到其他分支，在本地创建好分支A后，输入 git push -u origin 分支A ，然后就可以了。这时候github可以看到分支A。

21. 现在我们假设是另外一名开发者，git clone后默认处于master分支，但是如果远程仓库还有分支A的话，我们可以git checkout -b 分支A origin/分支A ，也就是在本地创建一个分支A，然后再从origin的分支A获取到本地仓库。

22. git pull 两个开发者如果修改同一个部分的代码，非常容易冲突，要经常pull和push操作。

23. git remote -v 查看仓库远程地址。

24. git branck -D 分支A 删除分支A。

# 奇淫技巧
1. 比较两个分支的区别这样写，比如master和test分支：https://github.com/MrChanGG/Hello-World/compare/master...test

2. 查看分支与前几天的差别，比如7天内：https://github.com/MrChanGG/Hello-World/compare/master@{7.day.ago}...master

3. 查看指定日期与现在的差别：https://github.com/MrChanGG/Hello-World/compare/master@{2013-01-01}...master

4. GitHub 的 issue 功能，对个人而言，就如同 TODO list 。比如commit message title, #1。如果添加上close,closes,closed,fix,fixes,fixed,resolve,resolves,resolved，就会相应关闭对应issue。比如commit message title, fix #n。

5. 有的人希望用diff或者patch格式来处理Pull Request，只需要这样写：https://github.com/MrChanGG/Hello-World/pull/2261.patch ，这里2261代表那个编号而已，举个例子用，后面添加上patch或者diff即可。

6. shift + /显示快捷键。

7. 评论中加:会启动补全表情功能。

8. 在Files Changed标签中可以看到Pull Request更改的文件内容以及前后差别，默认情况下会将空格的不同高亮显示，在URL末尾添加 ?w=1 可以不显示空格的差别。

# 基础概念实战总结

1. 分支并不是两个文件夹的意思，比如我们需要测试新功能的时候，就创建一个分支，拷贝一份master的代码到分支，然后写新功能去测试，如果测试没有问题，就把这个分支的代码合并到master上去，也就是相当于master是稳定版本，分支就是开发版本。

2. 还有一切操作在本地完成后，需要push上去，不然没有上传到Github。

3. 有个有意思的操作，在本地上有个文件是README.md(从master上clone下来的)，在master上和分支A上都有这个文件(同名但是里面内容不同)，当你在本地切换分支操作的时候，本地这个文件也会随着改变(也就是里面内容改变，不过记得要先把两个仓库的内容都要Pull下来)，很有意思哦，一定要注意分支的概念不同于文件夹。

4. 关于合并,合并首先是不能冲突的，比如一个文件的第一行，在master和分支A上的内容不一样，那就是有冲突。必须要消除冲突后才能合并，合并不是覆盖的意思。冲突解决后记得add和commit

5. 务必记住一定首先Pull下来，保证仓库文件和本地一样，然后全部在本地上执行，最后再push，毕竟有时候经常time out。

6. 在本地修改文件后(也进行了add和commit操作，但是没有push)，回到了master进行合并并且push，这时候只是master的内容被提交，也就是分支不同，最后push不是全部提交上去，push只是管当前的分支，所以要回到分支进行push，才可以把分支修改过的内容提交上去。

7. 关于回滚操作，也就是相当于穿梭回过去。打开git reflog会看到你最近的操作，reset命令加输入前面的哈希值就能退到这一步，文件内容也会相应发生变化。

8. 关于创建仓库问题，我们在本地Init后所有的操作都是在本地执行，这时候我们想把这些push到一个仓库。首先要去网上创建好一个仓库，务必记得不要初始化readme.md，这样对于我们来整合仓库会有麻烦，虽然是可以强制覆盖。

# PR（Pull Request）实战总结
1. Fork仓库

2. clone在本地。

3. 新增一个特性分支，然后修改内容，最后add,commit,push。

4. 打开网页版选择这个仓库，然后点击pull request后新建PR查看文件对比然后填写相关信息即可。

5. 关于仓库维护（也就是更新fork的仓库。1.首先设置远程仓库：git remote add upstream 原仓库名 2.获取最新数据：git fetch upstream 3将upstream/master与当前master合并：git merge upstream/master

# 接收到PR如何操作
1. 首先在本地上有我们自己原始仓库的数据

2. get remote add PR发送者 git@github.com:PR发送者/仓库名称.git 然后git fetch 

3. 然后我们自己创建一个分支用来测试建议的功能git  checkout -b pr1,这时候pr1的代码就是和原始的仓库一样。

4. git merge PR发送者/work，合并他们的内容进行测试。

5. 测试完后没问题可以删除pr1分支。

6. 接受PR建议：切换到master分支，然后合并PR发送者的分支，git diff origin/仓库名 确认本地与Github端代码差别。

7. 最后git push，PR状态会close。
