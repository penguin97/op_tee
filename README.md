1. 在本地创建 README 文件：在你的项目目录中创建一个名为 README.md 的文件。这个文件通常用于描述项目的目的、使用方法等信息。

echo "# My Project" > README.md

2. 初始化 Git 仓库：在项目目录中执行 git init 命令，这将初始化一个新的 Git 仓库。
git init

4. 添加 README 文件到 Git 仓库：使用 git add 命令将 README.md 文件添加到 Git 仓库。
git add README.md

6. 提交更改：使用 git commit 命令提交你的更改。这将创建一个新的提交，记录你添加 README.md 文件的操作。
git commit -m "add readme file"

8. 添加远程仓库：使用 git remote add 命令添加一个远程仓库。这个远程仓库通常是你在 GitHub 上创建的仓库。
git remote add origin https://github.com/yourusername/yourrepository.git

10. 推送更改到 GitHub：使用 git push 命令将你的更改推送到 GitHub。
git push -u origin master
