#### …or create a new repository on the command line
echo "# test_git" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/poiunet/test_git.git
git push -u origin main


#### …or push an existing repository from the command line
git remote add origin https://github.com/poiunet/test_git.git
git branch -M main
git push -u origin main

…or import code from another repository
You can initialize this repository with code from a Subversion, Mercurial, or TFS project.

git 命令
git init                        初始化项目
git add .                       将当前目录内容提交到暂存区
git commit -m ""                提交到远程仓库
git status                      查看项目状态
git log                         查看提交记录 
git log --author poiunet        查看poiunet的提交记录
git config --global --list       查看配置
git config --global user.name '***'  配置用户名
git config --global user.email '***' 配置email

git add ***.** 添加一个修改文件 未添加前 git status查看时文件名是红色，添加后是绿色 

 git config --global core.quotepath  false  执行完这个命令，git就不会对路径进行转换，显示原来完整的中文路径。