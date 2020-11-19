工作区-->暂存区-->本地库

每次工作区修改过的文件都要重新提交到本地库才能生效

如果想删除本地库的文件，那么需要删除工作区对应的文件，然后利用`add`、`commit`命令，就可以删除本地库的文件了

# 1 `add&commit`命令

`add`命令将代码从工作区放到暂存区

`git add + 文件名`

`commit`命令将代码从暂存区放到本地库

`git commit -m "注释" + 文件名`

提交到本地库的文件才受git管理

# 2 `status`命令

`status`命令可以查看工作区和暂存区的状态

`git status`

# 3 `log`命令

`git log`命令可以查看历史操作记录，当历史记录过多时，需要进行翻页

- 下一页：空格
- 上一页：b
- 退出：q

日志展示方式(由上至下，显示的越来越简洁)

1. `git log`
2. `git log --pretty=oneline`
3. `git  log --oneline`
4. `git reflog`：多了HEAD@{数字}信息，数字代表指针回退到对应版本的步数

# 4 `reset&mixed&soft`命令

`git reset --hard/mixed/soft + HEAD前的索引`命令：前进或者后退历史版本

- `hard`：如果回退了本地库的文件，工作区与暂存区的那个文件也会回退到历史版本
- `mixed`：如果回退了本地库的文件版本，暂存区的也跟着回退，但是工作区不回退
- `soft`：如果回退了本地库的文件版本，暂存区和工作区都不会跟着回退

# 5 `diff`命令

对比工作区和暂存区同一文件的内容相同不相同

`git diff + 文件名`

`git diff`不加文件名：对比工作区中和暂存区中所有文件的不同

`git diff HEAD + 文件名`：对比暂存区和本地库之间的同一文件有哪些不同，这里HEAD是指针索引号。

- 如果和指定历史版本对比，将HEAD改成指定历史版本对应的指针索引号
- 如果就是和HEAD指针指向的版本比较的话写HEAD

# 6 分支操作

- 查看分支命令：`git branch -v`

    在哪个分支上，分支名字前会有 * 号提示

- 创建分支命令：`git branch + 分支名`

- 切换分支：`git checkout + 分支名 `

- 合并分支：将子分支A合并到主分支master分支中
    1. 进入到主分支中：`git checkout master`
    2. 将子分支A中内容和主分支中内容合并：`git merge A`
    3. 在同一个文件同一位置修改的时候会出现冲突
        - 删去不想要的，留下想要的即可解决冲突
    4. 现在仍然处于`(master|MERGING)`合并状态中，然后进行commit操作，但是此处commit后不能带文件名，`git commit -m "注释"`后就不是合并状态了。
    
- 查看远程所有分支：`git branch -r`

- 查看本地和远程所有分支：`git branch -a`

- 删除本地分支：`git branch -d + 分支名`

- 重命名本地分支：`git branch -m 旧分支名+新分支名`

- 删除远程分支，删除后还需要推送到服务器

    - `git branch -d -r 远程分支名`
    - `git push origin:远程分支名`

