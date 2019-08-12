# git基础命令

## 1:本地git仓库处理

查看当前命令：git status       		--查看当前仓库里文件有没有进行操作。例如：红色的为工作区中修改，add之后变成绿色，与git添加了映射

删除文件：git rm 文件名      		-- 删除文件 例如：删除a.txt  git rm a.txt

单个添加文件：git add   文件    	—对当前这个文件进行添加

批量添加：git add .				--对所有改动和新增的文件进行添加

单个提交：git commit -m "提交备注" 文件名  --用于提交当前git的本地资源库

批量提交：git commit -m "提交备注"  --用于对本次的全部修改提交

添加并提交：git commit -am "提交备注" --用于添加映射并提交，新文件才会加映射(累似于git add)

撤销文件提交：git rm --cached file  —删除缓存和本地分支的文件提交，但是本地还会保留这个问题

## 2：分支处理

git branch -a:查看所有分支		--显示当前版本库和本地的所有分支

git branch -r :查看本地所有分支    	-- 查看当前的本地分支

git branch 分支名 ：创建分支   	-- 本地创建分支但是不会切换到这个分支上去

git branch -d 分支名：删除分支	--删除一个分支，对待处理的分支无法合并(例如这个分支存在未合并的)，如果强制删除 git branch --D 分支名

git branch -m 修改后的分支名 	—对当前的分支修改名称

## 3：与远程仓库交互

以下origin（默认为主机）

git clone git远程的网址：将远程代码下载到本地

git remote add origin git@172.0.0.1:test 与远程git仓库的项目建立连接  —与git仓库的项目建立连接，之后可以与远程仓库进行git命令操作

git remote show origin:显示远程仓库origin里的资源

git remote show：查看远程仓库

git remote :列出已经存在的远程分支

git remote -v:查看所有提交到远程仓库的地址：如

​		origin  https://githib.com/socore/test(dev)

​		origin https://github.com/socore/test(test）

git checkout -b 分支名：本地git仓库创建一个分支并检出 --本地创建一个新的分支并检出git远程仓库的代码

git checkout dev：切换分支到dev

git push -u origin dev:第一次推送每个分支的时候添加，与远程git仓库的对应分支建立连接，后面的push不用加-u参数了

git push origin(可以不加) dev:推送代码  --将本地的代码推送到git远程仓库的dev分支上去，多个主机的时候，需要推送到那个主机origin就是谁

git push origin dev:dev:本地分支与远程分支建立连接  —将本地git仓库的dev分支与远程git仓库的dev分支建立关联

git push:推送代码  —将当前分支提交的代码推送到远程git仓库中去

git merge 分支名 ：合并分支   —将dev分支与当前分支进行合并







