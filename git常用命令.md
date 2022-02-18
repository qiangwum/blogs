```sh
查看提交记录：
git log --pretty=oneline
查看某个commit的修改内容：
git show commit-id
回滚的commit版本：
git reset --hard HEAD^         回退到上个版本
git reset --hard HEAD~3        回退到前3次提交之前，以此类推，回退到n次提交之前
git reset --hard commit-id     退到/进到 指定commit的id码
在Git中，用【HEAD】表示当前版本
上一个版本就是【HEAD^】，上上一个版本就是【HEAD^^】，当然往上100个版本写100个^比较容易数不过来，所以写成【HEAD~100】
```




o_o ....😳
