# Git 指令用法详细文档 [^1]

## 一、引言

Git 是一个强大的分布式版本控制系统，广泛应用于软件开发项目的版本管理。它不仅能帮助开发者高效地跟踪代码变更、实现团队协作，还提供了一系列丰富的指令来满足各种复杂的开发场景需求。本文档将详细介绍 Git 的常用指令，包括基础操作和高阶用法，同时着重介绍 `stash` 操作，帮助你更好地掌握 Git 的使用技巧。

## 二、Git 安装与配置

### 2.1 安装 Git

不同操作系统安装 Git 的方式不同：

- **Windows**：从 [Git 官方网站](https://git-scm.com/downloads) 下载安装包，按照安装向导进行安装。
- **Linux**：使用包管理器进行安装，例如在 Ubuntu 上可以使用以下命令：
  
  ```bash
  sudo apt-get install git
  ```
- **macOS**：可以使用 Homebrew 进行安装：
  
  ```bash
  brew install git
  ```
  
  ### 2.2 配置 Git
  
  安装完成后，需要配置用户信息，包括用户名和邮箱：
  
  ```bash
  git config --global user.name "Your Name"
  git config --global user.email "your.email@example.com"
  ```
  
  可以使用以下命令查看配置信息：
  
  ```bash
  git config --list
  ```
  
## 三、基本概念
  
### 3.1 仓库（Repository）
  
  仓库是 Git 中存储项目文件和版本历史的地方，分为本地仓库和远程仓库。本地仓库位于开发者的本地计算机上，远程仓库通常位于服务器上，用于团队成员之间的协作。
  
### 3.2 工作区（Working Directory）
  
  工作区是开发者实际进行文件编辑的地方，也就是项目文件夹。
  
### 3.3 暂存区（Staging Area）
  
  暂存区是一个中间区域，用于暂时存放将要提交到仓库的文件。可以选择部分文件或全部文件添加到暂存区。
  
### 3.4 本地仓库（Local Repository）
  
  本地仓库存储了项目的所有版本历史和元数据。提交操作将暂存区的文件保存到本地仓库。
  
### 3.5 远程仓库（Remote Repository）
  
  远程仓库位于服务器上，团队成员可以通过网络访问和协作。常见的远程仓库托管平台有 GitHub、GitLab 等。
  以下是这些概念之间的关系图：
  ![Git 基本概念关系图](https://git-scm.com/book/en/v2/images/areas.png)
  
## 四、常用 Git 指令
  
### 4.1 初始化仓库
  
  在项目文件夹中使用以下命令初始化一个新的 Git 仓库：
  
  ```bash
  git init
  ```
  
  该命令会在当前目录下创建一个 `.git` 文件夹，用于存储 Git 的元数据。
  
### 4.2 克隆仓库
  
  如果要从远程仓库获取项目代码，可以使用 `git clone` 命令：
  
  ```bash
  git clone <repository-url>
  ```
  
  例如，克隆一个 GitHub 上的项目：
  
  ```bash
  git clone https://github.com/username/repository.git
  ```
  
### 4.3 添加文件到暂存区
  
  使用 `git add` 命令将文件添加到暂存区：
  
  ```bash
  # 添加单个文件
  git add file.txt
  # 添加所有文件
  git add .
  ```
  
### 4.4 提交文件到本地仓库
  
  使用 `git commit` 命令将暂存区的文件提交到本地仓库：
  
  ```bash
  git commit -m "Commit message"
  ```
  
  其中，`-m` 选项用于指定提交信息，描述本次提交的内容。
  
### 4.5 查看文件状态
  
  使用 `git status` 命令查看工作区和暂存区的文件状态：
  
  ```bash
  git status
  ```
  
  该命令会显示哪些文件被修改、哪些文件已添加到暂存区等信息。
  
### 4.6 查看提交历史
  
  使用 `git log` 命令查看提交历史：
  
  ```bash
  git log
  ```
  
  可以使用一些选项来格式化输出，例如只显示最近的几次提交：
  
  ```bash
  git log -n 3
  ```
  
### 4.7 查看文件差异
  
  使用 `git diff` 命令查看文件的差异：
  
  ```bash
  # 查看工作区与暂存区的差异
  git diff
  # 查看暂存区与本地仓库的差异
  git diff --staged
  ```
  
### 4.8 撤销修改
  
  **撤销工作区的修改**：
  
  ```bash
  git checkout -- file.txt
  ```
  
  该命令会将工作区的文件恢复到上次提交的状态。
  
  **撤销暂存区的文件**：
  
  ```bash
  git reset HEAD file.txt
  ```
  
  该命令会将文件从暂存区移除，但不会改变工作区的内容。
  
### 4.9 分支操作
  
#### 创建分支
  
  使用 `git branch` 命令创建新的分支：
  
  ```bash
  git branch new-branch
  ```
  
#### 切换分支
  
  使用 `git checkout` 命令切换到指定的分支：
  
  ```bash
  git checkout new-branch
  ```
  
  也可以使用以下命令创建并切换到新分支：
  
  ```bash
  git checkout -b new-branch
  ```
  
#### 查看分支
  
  使用 `git branch` 命令查看本地分支：
  
  ```bash
  git branch
  ```
  
  使用 `git branch -r` 命令查看远程分支，使用 `git branch -a` 命令查看所有分支。
  
#### 合并分支
  
  使用 `git merge` 命令将一个分支的修改合并到当前分支：
  
  ```bash
  git merge other-branch
  ```
  
#### 删除分支
  
  使用 `git branch -d` 命令删除本地分支：
  
  ```bash
  git branch -d branch-name
  ```
  
### 4.10 远程仓库操作
  
#### 添加远程仓库
  
  使用 `git remote add` 命令添加远程仓库：
  
  ```bash
  git remote add origin <repository-url>
  ```
  
  其中，`origin` 是远程仓库的别名，通常用于表示默认的远程仓库。
  
#### 推送本地分支到远程仓库
  
  使用 `git push` 命令将本地分支的修改推送到远程仓库：
  
  ```bash
  git push origin branch-name
  ```
  
#### 拉取远程仓库的修改
  
  使用 `git pull` 命令拉取远程仓库的修改并合并到本地分支：
  
  ```bash
  git pull origin branch-name
  ```
  
#### 远程修改有新提交
  
  使用 `git pull --rebase`命令拉取远程仓库的修改并合并到本地分支
  
  ```bash
  git pull --rebase origin branch-name
  ```
  
#### 查看远程仓库信息
  
  使用 `git remote -v` 命令查看远程仓库的信息：
  
  ```bash
  git remote -v
  ```
  
## 五、高阶 Git 指令
  
### 5.1 Git Rebase（变基）
  
#### 5.1.1 基本概念
  
  `git rebase` 是一种将一个分支的修改应用到另一个分支上的方法。与 `git merge` 不同，`git rebase` 会将分支的提交历史整理成一条直线，使提交历史更加清晰。
  
#### 5.1.2 用法示例
  
  假设我们有两个分支：`master` 和 `feature`，`feature` 分支是从 `master` 分支派生出来的。现在 `master` 分支有了新的提交，我们想将 `feature` 分支的修改应用到最新的 `master` 分支上，可以使用以下命令：
  
  ```bash
  # 切换到 feature 分支
  git checkout feature
  # 将 feature 分支的修改变基到 master 分支上
  git rebase master
  ```
  
  上述命令会将 `feature` 分支的所有提交依次应用到 `master` 分支的最新提交之后。
  
#### 5.1.3 处理冲突
  
  在变基过程中，如果出现冲突，Git 会暂停变基操作，并提示你解决冲突。解决冲突后，使用以下命令继续变基：
  
  ```bash
  git add <resolved-file>
  git rebase --continue
  ```
  
  如果想放弃变基操作，可以使用以下命令：
  
  ```bash
  git rebase --abort
  ```
  
#### 5.1.4 图示说明
  
##### 1. 初始状态
  
  假设我们有两个分支：`master` 和 `feature`。`feature` 分支是从 `master` 分支的某个提交（如 `B`）派生出来的。在 `feature` 分支创建之后，`master` 分支又有了新的提交（`C`、`D`），同时 `feature` 分支也有自己的提交（`E`、`F`）。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-38-57-image.png)
  
##### 2. 切换到 `feature` 分支
  
  要将 `feature` 分支的修改基于 `master` 分支的最新状态，首先需要切换到 `feature` 分支。
  
  ```bash
  git checkout feature
  ```
  
  此时 `HEAD` 指针指向 `feature` 分支的最新提交 `F`。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-39-06-image.png)
  
##### 3. 执行 `git rebase master`
  
  当执行 `git rebase master` 命令时，Git 会将 `feature` 分支的提交（`E`、`F`）暂时保存起来，然后将 `feature` 分支的指针移动到 `master` 分支的最新提交 `D` 上，最后再将之前保存的提交（`E`、`F`）依次应用到 `D` 之后，形成新的提交 `E'`、`F'`。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-39-13-image.png)
  
##### 4. 处理冲突情况
  
  在变基过程中，如果出现冲突，Git 会暂停变基操作。例如，在应用 `E` 提交（变成 `E'`）时出现冲突，图示如下：
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-39-21-image.png)
  
  解决冲突后，执行 `git add <resolved-file>` 和 `git rebase --continue`，继续变基操作，直到所有提交都应用完毕。
  
##### 5. 变基完成后的状态
  
  当所有提交都成功应用后，`feature` 分支的提交历史就变成了一条线性的历史，基于 `master` 分支的最新状态。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-39-29-image.png)
  
  通过以上图示，你可以清晰地看到 `git rebase` 命令是如何将一个分支的修改应用到另一个分支上，并整理提交历史的。同时，也能了解到在变基过程中遇到冲突时的处理方式和状态变化。
  
### 5.2 Git Cherry-pick（挑选提交）
  
#### 5.2.1 基本概念
  
  `git cherry-pick` 用于从一个分支中挑选一个或多个提交，并将这些提交应用到当前分支上。这在需要将某个特定的提交从一个分支复制到另一个分支时非常有用。
  
#### 5.2.2 用法示例
  
  假设我们有两个分支：`master` 和 `feature`，`feature` 分支上有一个提交 `abc123`，我们想将这个提交应用到 `master` 分支上，可以使用以下命令：
  
  ```bash
  # 切换到 master 分支
  git checkout master
  # 挑选 feature 分支上的提交 abc123 应用到当前分支
  git cherry-pick abc123
  ```
  
  如果要挑选多个提交，可以依次列出提交的哈希值：
  
  ```bash
  git cherry-pick abc123 def456
  ```
  
#### 5.2.3 处理冲突
  
  与 `git rebase` 类似，在 `git cherry-pick` 过程中如果出现冲突，需要手动解决冲突，然后使用以下命令继续：
  
  ```bash
  git add <resolved-file>
  git cherry-pick --continue
  ```
  
  如果想放弃当前的 `cherry-pick` 操作，可以使用以下命令：
  
  ```bash
  git cherry-pick --abort
  ```
  
#### 5.2.4 图示说明
  
##### 1. 初始状态
  
  假设我们有两个分支：`master` 和 `feature`。`master` 分支上有一系列提交（`A`、`B`、`C`），`feature` 分支从 `master` 分支的 `B` 提交处派生出来，并且有自己的提交（`D`、`E`）。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-14-18-image.png)
  
##### 2. 切换到目标分支
  
  现在我们想要把 `feature` 分支上的 `E` 提交应用到 `master` 分支上。首先，我们需要切换到 `master` 分支。
  
  ```bash
  git checkout master
  ```
  
  切换后，`HEAD` 指针指向 `master` 分支的最新提交 `C`。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-14-25-image.png)
  
##### 3. 执行 `git cherry-pick`
  
  接着执行 `git cherry-pick E`（这里 `E` 代表提交 `E` 的哈希值），Git 会将 `E` 提交的修改复制到 `master` 分支上，并在 `master` 分支上创建一个新的提交 `E'`（虽然内容和 `E` 一样，但哈希值不同）。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-14-56-image.png)
  
##### 4. 多次 `cherry-pick` 场景
  
  如果我们还想把 `feature` 分支上的 `D` 提交也应用到 `master` 分支上，再次执行 `git cherry-pick D`。此时，Git 会在 `E'` 提交之后创建一个新的提交 `D'`。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-15-04-image.png)
  
##### 5. 处理冲突情况
  
  在 `cherry-pick` 过程中，如果出现冲突，Git 会暂停操作并提示你解决冲突。假设在 `cherry-pick E` 时出现冲突，图示如下：
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-15-16-image.png)
  
  解决冲突后，执行 `git add <resolved-file>` 和 `git cherry-pick --continue`，完成 `cherry-pick` 操作，最终会生成新提交 `E'`。
  这些图示清晰地展示了 `git cherry-pick` 的工作流程，包括正常操作和冲突处理的情况，帮助你更好地理解该命令的使用和效果。
  
### 5.3 Git Checkout 的高阶用法
  
#### 5.3.1 切换到指定的提交
  
  除了切换分支，`git checkout` 还可以用于切换到指定的提交。例如，要切换到提交 `abc123` 的状态，可以使用以下命令：
  
  ```bash
  git checkout abc123
  ```
  
  此时，HEAD 会处于分离状态，即不指向任何分支。如果需要在这个状态下进行修改并保存，可以创建一个新的分支：
  
  ```bash
  git checkout -b new-branch
  ```
  
#### 5.3.2 恢复文件到指定版本
  
  可以使用 `git checkout` 命令将文件恢复到指定版本。例如，将 `file.txt` 文件恢复到提交 `abc123` 时的状态：
  
  ```bash
  git checkout abc123 -- file.txt
  ```
  
#### 5.3.3 图示说明
  
##### 1. 基本分支切换场景
  
  **初始状态**
  
  假设当前仓库有两个分支：`master` 和 `feature`，`HEAD` 指针指向 `master` 分支，工作区和暂存区的内容与 `master` 分支的最新提交一致。可以用以下图示表示：
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-07-14-image.png)
  
  **执行 `git checkout feature`** 
  
  当执行 `git checkout feature` 命令时，`HEAD` 指针会从 `master` 分支切换到 `feature` 分支，工作区和暂存区的内容会更新为 `feature` 分支最新提交的内容。图示如下：
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-07-32-image.png)
  
##### 2. 切换到指定提交（分离 HEAD 状态）
  
  **初始状态**
  
  同样假设仓库有 `master` 和 `feature` 分支，当前 `HEAD` 指向 `master` 分支的最新提交。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-07-51-image.png)
  
  **执行 `git checkout commit B**
  
  当执行 `git checkout <commit B 的哈希值>` 时，`HEAD` 会直接指向指定的提交 `commit B`，此时处于分离 `HEAD` 状态，即 `HEAD` 不再指向任何分支。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-08-07-image.png)
  
  **从分离 `HEAD` 状态创建新分支**
  
  如果在分离 `HEAD` 状态下进行了一些修改并想保存为一个新分支，可以执行 `git checkout -b new-branch`。这时会创建一个新分支 `new-branch`，并让 `HEAD` 指向这个新分支。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-08-22-image.png)
  
##### 3. 使用 `git checkout` 恢复文件到指定版本
  
  **初始状态**
  
  假设当前工作区的 `file.txt` 文件相对于 `master` 分支的最新提交有了修改，暂存区也有一些文件。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-08-40-image.png)
  
  **执行 `git checkout commit A -- file.txt**
  
  执行 `git checkout <commit A 的哈希值> -- file.txt` 命令后，工作区的 `file.txt` 文件会恢复到 `commit A` 时的状态。
  
  ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-14-08-53-image.png)
  
  这些图示和说明展示了 `git checkout` 命令在不同场景下的作用和影响，有助于理解该命令的工作原理。
  
## 六、Git Stash 操作指南
  
### 6.1 基本概念
  
  `git stash` 用于将当前工作区和暂存区的修改临时保存起来，使工作区恢复到上一次提交的干净状态。这在你需要切换到其他分支处理紧急任务，但又不想提交当前未完成的工作时非常有用。
  
### 6.2 常用命令
  
#### 6.2.1 `git stash`
  
  将当前工作区和暂存区的修改保存到栈中，工作区恢复到干净状态。
  
  ```bash
  git stash
  ```
  
  也可以使用以下命令添加备注信息：
  
  ```bash
  git stash save "Work in progress on feature X"
  ```
  
#### 6.2.2 `git stash list`
  
  查看 stash 栈中的所有记录：
  
  ```bash
  git stash list
  ```
  
  输出示例：
  
  ```
  stash@{0}: On feature-branch: Work in progress on feature X
  stash@{1}: On master: Fixed a minor bug
  ```
  
#### 6.2.3 `git stash apply`
  
  应用 stash 栈中的某条记录，默认应用最近的一条记录：
  
  ```bash
  git stash apply
  ```
  
  如果要应用指定的记录，可以使用记录的名称：
  
  ```bash
  git stash apply stash@{1}
  ```
  
  应用记录后，stash 记录不会从栈中删除。
  
#### 6.2.4 `git stash pop`
  
  应用 stash 栈中的某条记录，并将该记录从栈中删除，默认应用并删除最近的一条记录：
  
  ```bash
  git stash pop
  ```
  
  如果要应用并删除指定的记录，可以使用记录的名称：
  
  ```bash
  git stash pop stash@{1}
  ```
  
#### 6.2.5 `git stash drop`
  
  删除 stash 栈中的某条记录，默认删除最近的一条记录：
  
  ```bash
  git stash drop
  ```
  
  如果要删除指定的记录，可以使用记录的名称：
  
  ```bash
  git stash drop stash@{1}
  ```
  
#### 6.2.6 `git stash clear`
  
  清空 stash 栈中的所有记录：
  
  ```bash
  git stash clear
  ```
  
### 6.3 示例场景
  
  假设你正在 `feature` 分支上开发新功能，工作进行到一半时，需要紧急切换到 `master` 分支修复一个 bug，但你不想提交当前未完成的工作。可以按以下步骤操作：
  1. 保存当前工作：
   
   ```bash
   git stash
   ```
   
  此时工作区恢复到干净状态。
  2. 切换到 `master` 分支并修复 bug：
   
   ```bash
   git checkout master
   # 修复 bug
   git add .
   git commit -m "Fixed a critical bug"
   ```
  3. 切换回 `feature` 分支并恢复工作：
   
   ```bash
   git checkout feature
   git stash pop
   ```
   
   现在你可以继续之前未完成的工作。
   
### 6.4 图示说明
   
#### 6.4.1 初始状态
   
   假设你正在一个 `feature` 分支上进行开发，工作区有一些修改还未提交，暂存区也有部分文件已添加。此时的状态可以用下面的图来表示：
   
   ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-13-25-59-image.png)
   
#### 6.4.2 执行 `git stash`
   
   当你执行 `git stash` 命令后，工作区和暂存区的修改会被保存到一个 stash 栈中，工作区和暂存区恢复到上一次提交时的干净状态。
   
   ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-11-12-35-image.png)
   
#### 6.4.3 切换分支处理其他任务
   
   此时你可以自由地切换到其他分支（如 `master` 分支）处理紧急任务，因为工作区是干净的，不会受到之前未完成工作的影响。
   
   ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-13-26-25-image.png)
   
#### 6.4.4 执行 `git stash pop`
   
   当你完成其他任务，切换回 `feature` 分支后，执行 `git stash pop` 命令，stash 栈中最近的一条记录会被应用到工作区和暂存区，并且该记录会从 stash 栈中删除。
   
   ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-13-26-40-image.png)
   
#### 6.4.5 多次 stash 情况
   
   如果你多次执行 `git stash` 命令，stash 栈中会有多个记录，栈顶的记录是最近保存的。例如，执行了两次 `git stash` 后：
   
   ![](https://github.com/penguin97/op_tee/blob/master/git/images/2025-02-08-13-26-59-image.png)
   
   当执行 `git stash pop stash@{1}` 时，会应用 `stash@{1}` 的记录并从栈中删除它，栈中的其他记录顺序会相应调整。
   
## 七、总结
   
   本文档全面介绍了 Git 的基本概念、常用指令、高阶指令以及 `stash` 操作。掌握这些知识可以帮助开发者更高效地使用 Git 进行版本管理，应对各种复杂的开发场景。同时，在使用这些指令时，要注意处理可能出现的冲突，确保代码的一致性和正确性。
## 八、引用
   [^1]:[Git详细操作手册]()
