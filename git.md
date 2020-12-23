# git使用教程
## 创建仓库
git命令必须在仓库目录内执行
1. 创建一个空目录并进入
` $ mkdir learngit`  
` $ cd learngit`

2. 将这个目录变成git可以管理的仓库  
`$ git init`  
3. 将一个文件添加到仓库
` $ git add readme.txt`
4. 将这个文件提交到仓库  
` $ git commit -m "wrote a readme file"`  
-m后面是对本次修改的说明  
# 对仓库内容就行修改  
用一下命令可以掌握工作区状态，他会告诉我们文件有没有修改过
` git status`
以下命令可以查看修改内容  
` git diff readme.txt`

1. 版本回退
以下命令会告诉我们commit的历史记录，它会显示从最近到最远的提交版本  
` git log`  
在git中，用HEAD表示当前版本，上一个版本是HEAD^,上上个版本是HEAD^^,往上100个版本就是HEAD~100  
回退上一个版本如下  
` git reset --hard HEAD^`  
显然，当回退到上一个版本之后，刚才的当前版本已经不会在git log中看到了。但扔可以通过commit id回到刚才的那个版本（7c0b9b为那个版本git版本号的前几位）  
` git reset --hard  7c0b9b`  
但是那个版本的版本号找不到或者不记得了怎么办？不要紧，git记录了我们的每一次命令,通过如下命令行查看命令历史，就可以找到我们想重返的版本的id  
`$ git reflog `  
# 2. 工作区和暂存区
Git管理的文件分为工作区和版本库(.git隐藏目录)。版本库又分为暂存区stage和git为我们创建的第一个分支master。
我们把文件添加到git版本库分为两步：  
* 用git add把文件修改添加到暂存区
* 用git commit将暂存区的内容添加到分支  
可以理解为：将需要提交的修改先全放到暂存区，再一并提交修改。  
# 3. 撤销修改和删除文件
` $ git checkout --readme.txt`
有两种情况：
* readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一摸一样的状态；
* readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存后的状态。


