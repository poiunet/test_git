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
git init            初始化项目
git add .           将当前目录内容提交到暂存区
git commit -m ""    提交到远程仓库
git status          查看项目状态
git log             查看提交记录 
    