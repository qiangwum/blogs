1. 将自己新写的代码备份到其他地方。
2. 删除本地项目里自己新写的代码。
3. git pull 下拉代码，使本地代码与远端代码一致。
4. 重新上传代码 
  git add .
  git commit -m "fix bug"
  git push

  git clone git://github.com/schacon/grit.git 从服务器上将代码给拉下来
  git commit -m "This is the message describing the commit" 添加commit信息
  git commit -a -a是代表add，把所有的change加到git index里然后再commit
  git commit -a -v 一般提交命令
  git log 看你commit的日志
  git diff 查看尚未暂存的更新
  git rm a.a 移除文件(从暂存区和工作区中删除)
  git rm --cached a.a 移除文件(只从暂存区中删除)
  git commit -m "remove" 移除文件(从Git中删除)
  git rm -f a.a 强行移除修改后文件(从暂存区和工作区中删除)
  git diff --cached 或 $ git diff --staged 查看尚未提交的更新
  git stash push 将文件给push到一个临时空间中

git stash pop 将文件从临时空间pop下来

git remote add origin git@github.com:qiangwum/blogs.git

git push origin master 将本地项目给提交到服务器中

git pull 本地与服务器端同步

git push (远程仓库名) (分支名) 将本地分支推送到服务器上去。

git push origin serverfix:awesomebranch

git fetch 相当于是从远程获取最新版本到本地，不会自动merge
git commit -a -m "log_message" (-a是提交所有改动，-m是加入log信息) 本地修改同步至服务器端 ：
git branch branch_0.1 master 从主分支master创建branch_0.1分支
git branch -m branch_0.1 branch_1.0 将branch_0.1重命名为branch_1.0
git checkout branch_1.0/master 切换到branch_1.0/master分支
du -hs

git branch 删除远程branch
git push origin :branch_remote_name

git branch -r -d branch_remote_name

初始化版本库，并提交到远程服务器端
mkdir WebApp
cd WebApp
git init 本地初始化
touch README
git add README 添加文件
git commit -m 'first commit'
git remote add origin git@github.com:qiangwum/blogs.git
