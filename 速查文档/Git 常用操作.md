# Git 常用操作

## 信息来源

博客园 [Git 常用命令及方法大全](https://www.cnblogs.com/miracle77hp/articles/11163532.html)

[Git 官方文档](https://git-scm.com/book/zh/v2)

## 代码库操作

### 初始化

1. 在当前目录新建一个 Git 代码库

   ```bash
   git init 
   ```

2. 新建一个目录，将其初始化为 Git 代码库

   ```bash
   git init [project-name]
   ```

3. 下载一个项目和它的整个代码历史

   ```bash
   git clone [url]
   ```

### 增加/删除文件

1. 添加指定文件到暂存区

   ```bash
   git add [file1] [file2] ...
   ```

2. 添加指定目录到暂存区，包括子目录

   ```bash
   git add [dir]
   ```

3. 添加当前目录的所有文件到暂存区

   ```bash
   git add .
   ```

4. 添加每个变化前，都会要求确认。对于同一个文件的多处变化，可以实现分次提交

   ```bash
   git add -p
   ```

5. 删除工作区文件，并且将这次删除放入暂存区

   ```bash
   git rm [file1] [file2] ...
   ```

6. 停止追踪指定文件，但该文件会保留在工作区

   ```bash
   git rm --cached [file]
   ```

7. 改名文件，并且将这个改名放入暂存区

   ```bash
   git mv [file-original] [file-renamed]
   ```

### 代码提交

1. 提交暂存区到仓库区

   ```bash
   git commit -m [message]
   ```

2. 提交暂存区的指定文件到仓库区

   ```bash
   git commit [file1] [file2] ... -m [message]
   ```

3. 提交工作区自上次 commit 之后的变化，直接到仓库区

   ```bash
   git commit -a
   ```

4. 提交时显示所有 diff 信息

   ```bash
   git commit -v
   ```

5. 使用一次新的 commit，替代上一次提交。如果代码没有任何新变化，则用来改写上一次 commit 的提交信息

   ```bash
   git commit --amend -m [message]
   ```

6. 重做上一次 commit，并包括指定文件的新变化

   ```bash
   git commit --amend [file1] [file2] ...
   ```

### 分支

1. 列出所有本地分支

   ```bash
   git branch
   ```

2. 列出所有远程分支

   ```bash
   git branch -r
   ```

3. 列出所有本地分支和远程分支

   ```bash
   git branch -a
   ```

4. 新建一个分支，但依然停留在当前分支

   ```bash
   git branch [branch-name]
   ```

5. 新建一个分支，并切换到该分支

   ```bash
   git checkout -b [branch]
   ```

6. 新建一个分支，指向指定 commit

   ```bash
   git branch [branch] [commit]
   ```

7. 新建一个分支，与指定的远程分支建立追踪关系

   ```bash
   git branch --track [branch] [remote-branch]
   ```

8. 切换到指定分支，并更新工作区

   ```bash
   git checkout [branch-name]
   ```

9. 切换到上一个分支

   ```bash
   git checkout -
   ```

10. 建立追踪关系，在现有分支与指定的远程分支之间

    ```bash
    git branch --set-upstream [branch] [remote-branch]
    ```

11. 合并指定分支到当前分支

    ```bash
    git merge [branch]
    ```

12. 选择一个 commit，合并进当前分支

    ```bash
    git cherry-pick [commit]
    ```

13. 删除分支

    ```bash
    git branch -d [branch-name]
    ```

14. 删除远程分支

    ```bash
    git push origin --delete [branch-name]
    git branch -dr [remote/branch]
    ```

### 标签

1. 列出所有 tag

   ```bash
   git tag
   ```

2. 新建一个 tag 在当前 commit

   ```bash
   git tag [tag]
   ```

3. 新建一个 tag 在指定 commit

   ```bash
   git tag [tag] [commit]
   ```

4. 删除本地 tag

   ```bash
   git tag -d [tag]
   ```

5. 删除远程 tag

   ```bash
   git push origin :refs/tags/[tagName]
   ```

6. 查看 tag 信息

   ```bash
   git show [tag]
   ```

7. 提交指定 tag

   ```bash
   git push [remote] [tag]
   ```

8. 提交所有 tag

   ```bash
   git push [remote] --tags
   ```

9. 新建一个分支，指向某个 tag

   ```bash
   git checkout -b [branch] [tag]
   ```

### 查看信息

1. 显示有变更的文件

   ```bash
   git status
   ```

2. 显示当前分支的版本历史

   ```bash
   git log
   ```

3. 显示 commit 历史，以及每次 commit 发生变更的文件

   ```bash
   git log --stat
   ```

4. 搜索提交历史，根据关键词

   ```bash
   git log -S [keyword]
   ```

5. 显示某个 commit 之后的所有变动，每个 commit 占据一行

   ```bash
   git log [tag] HEAD --pretty=format:%s
   ```

6. 显示某个 commit 之后的所有变动，其"提交说明"必须符合搜索条件

   ```bash
   git log [tag] HEAD --grep feature
   ```

7. 显示某个文件的版本历史，包括文件改名

   ```bash
   git log --follow [file]
   git whatchanged [file]
   ```

8. 显示指定文件相关的每一次 diff

   ```bash
   git log -p [file]
   ```

9. 显示过去 5 次提交

   ```bash
   git log -5 --pretty --oneline
   ```

10. 显示所有提交过的用户，按提交次数排序

    ```bash
    git shortlog -sn
    ```

11. 显示指定文件是什么人在什么时间修改过

    ```bash
    git blame [file]
    ```

12. 显示暂存区和工作区的差异

    ```bash
    git diff
    ```

13. 显示暂存区和上一个 commit 的差异

    ```bash
    git diff --cached [file]
    ```

14. 显示工作区与当前分支最新 commit 之间的差异

    ```bash
    git diff HEAD
    ```

15. 显示两次提交之间的差异

    ```bash
    git diff [first-branch]...[second-branch]
    ```

16. 显示今天你写了多少行代码

    ```bash
    git diff --shortstat "@{0 day ago}"
    ```

17. 显示某次提交的元数据和内容变化

    ```bash
    git show [commit]
    ```

18. 显示某次提交发生变化的文件

    ```bash
    git show --name-only [commit]
    ```

19. 显示某次提交时，某个文件的内容

    ```bash
    git show [commit]:[filename]
    ```

20. 显示当前分支的最近几次提交

    ```bash
    git reflog
    ```

### 远程同步

1. 下载远程仓库的所有变动

   ```bash
   git fetch [remote]
   ```

2. 显示所有远程仓库

   ```bash
   git remote -v
   ```

3. 显示某个远程仓库的信息

   ```bash
   git remote show [remote]
   ```

4. 增加一个新的远程仓库，并命名

   ```bash
   git remote add [shortname] [url]
   ```

5. 取回远程仓库的变化，并与本地分支合并

   ```bash
   git pull [remote] [branch]
   ```

6. 上传本地指定分支到远程仓库

   ```bash
   git push [remote] [branch]
   ```

7. 强行推送当前分支到远程仓库，即使有冲突

   ```bash
   git push [remote] --force
   ```

8. 推送所有分支到远程仓库

   ```bash
   git push [remote] --all
   ```

### 撤销

1. 恢复暂存区的指定文件到工作区

   ```bash
   git checkout [file]
   ```

2. 恢复某个 commit 的指定文件到暂存区和工作区

   ```bash
   git checkout [commit] [file]
   ```

3. 恢复暂存区的所有文件到工作区

   ```bash
   git checkout .
   ```

4. 重置暂存区的指定文件，与上一次 commit 保持一致，但工作区不变

   ```bash
   git reset [file]
   ```

5. 重置暂存区与工作区，与上一次 commit 保持一致

   ```bash
   git reset --hard
   ```

6. 重置当前分支的指针为指定 commit，同时重置暂存区，但工作区不变

   ```bash
   git reset [commit]
   ```

7. 重置当前分支的 HEAD 为指定 commit，同时重置暂存区和工作区，与指定 commit 一致

   ```bash
   git reset --hard [commit]
   ```

8. 重置当前 HEAD 为指定 commit，但保持暂存区和工作区不变

   ```bash
   git reset --keep [commit]
   ```

9. 新建一个 commit，用来撤销指定 commit 后者的所有变化都将被前者抵消，并且应用到当前分支

   ```bash
   git revert [commit]
   ```

10. 暂时将未提交的变化移除，稍后再移入

    ```bash
    git stash
    git stash pop
    ```

### 子模块

1. 添加已有的仓库为子模块到当前仓库，Path 为自定义文件夹名，可选

   ```bash
   git submodule add <URL> [Path]
   ```

2. 克隆包含子模块的远程仓库

   ```bash
   git clone --recurse-submodules <URL>
   ```

3. 初始化子模块（使用 git clone 克隆了含有子模块的仓库但没有文件需要先初始化再更新子模块内容）

   ```bash
   git submodule init
   ```

4. 检出子模块的内容，可以指定子模块名称来更新特定子模块

   ```bash
   git submodule update <submodule-name>
   ```

5. 初始化、抓取并检出任何嵌套的子模块

   ```bash
   git submodule update --init --recursive
   ```

6. 获取子模块远端更新，可以指定子模块名称来更新特定子模块

   ```bash
   git submodule update --remote <submodule-name>
   ```

7. 递归获取所有子模块远端更新（不更新到 .gitmodules）

   ```bash
   git submodule update --recursive --remote  <submodule-name>
   ```

8. 拉取所有子模块更新（更新.gitmodules）

   ```bash
   git pull --recurse-submodules
   ```

9. 将新的 URL 复制到本地配置中，当子模块的 URL 有更新时

   ```bash
   git submodule sync --recursive
   ```

### 其他

生成一个可供发布的压缩包

```bash
git archive
```
