# Linux等工具 面经

# Linux

- #### 查看文件内容
   - `cat`：一次性显示整个文件的内容。
   - `more`和`less`：分页显示文件内容，`less`命令比`more`更高级，支持向前和向后浏览。
   - `head`：显示文件的开始部分，默认显示前10行。
   - `tail`：显示文件的末尾部分，默认显示最后10行。常用于查看日志文件
- #### 如何查看文件行数
   - `wc -l 文件名`
- #### 有一个很大的文件，想看前几行，用什么指令
   - `head -n 10 文件名`  前10行
- **查看文件的某一列**
   - `cut -d ',' -f 2 data.csv`  cut -d'分隔符' -f第几列 文件名
   - `awk '{print $N}' filename.txt` N是列数-空格或制表符分隔的列
- #### 查看系统状态
   - top：查看系统运行进程及其资源占用情况，可以实时更新显示。-
   - vmstat：查看系统的内存、磁盘、CPU 等资源的使用情况和进程信息。
   - iostat：查看磁盘 I/O 活动情况，显示磁盘读写速度、磁盘利用率等信息。
   - sar：系统活动报告，可以查看 CPU、内存、磁盘、网络等性能指标历史记录。
   - netstat：查看网络连接状态、网络接口的状态和统计信息。
   - ifconfig：查看网络接口的配置信息，包括 IP 地址、MAC 地址等。
   - free：查看内存使用情况，包括内存总量、空闲内存、已用内存等
- #### 创建文件
   1. **使用`touch`命令**:
      - `touch`命令的主要用途是修改文件的访问和修改时间戳。如果指定的文件不存在，`touch`会创建一个空文件。例如，执行`touch newfile.txt`会创建一个名为`newfile.txt`的空文件。这种方法简单快捷，特别适合在需要快速创建空文件进行测试或其他用途时使用。
   2. **使用重定向操作符`>`**:
      - 在Linux中，重定向操作符`>`可以用于将命令的输出重定向到文件中。如果目标文件不存在，系统会自动创建该文件。例如，执行`echo "Hello, World!" > hello.txt`不仅会创建`hello.txt`文件，还会写入文本`Hello, World!`。如果只是想创建一个空文件，可以使用`> newfile.txt`，这会创建一个名为`newfile.txt`的空文件，但这种用法不如`touch`命令直观。

## Git

- #### 初始化
   - `git init` 能够在当前目录生成.git，其中包含所有必需的仓库文件
- #### 分支管理-git branch, git checkout, git merge
      - 假设分支名为member、 new
   - `git branch -a` 列出本地和远程所有分支、`-r` 列出远程、`git branch`  列出本地分支、`git branch new` 创建新分支
   - `git branch -d member | git branch -D member` 删除分支，D为强制删除而d则需要分支合并后才能删除
      - git checkout的部分功能在2.23后被拆分为switch和restore
   - `git switch member`  切换 `-c new` 则是创建后切换
   - `git restore "file"` 撤销对“file"的修改，`git restore --staged`  从暂存恢复到工作目录、不影响当前修改
   - 如果需要将member合并到master :  `git checkout(switch) master  | git merge branch`
- #### 变更提交与回退
   - `git revert <commit>` 此处commit是哈希值(a1b2c3d4) ,提供一个新的commit来撤回指定a1b2c3d4的修改，不影响历史记录
   - `git reset` 用于重置当前HEAD到指定提交，`--hard` 会将一切都重置到指定commit舍弃一切更改，`--soft` 会将保留改为待提交
- #### 远程操作
   - `git push` 用于将本地分支的更新推送到远程仓库
   - `git pull` 是将远程仓库获取最新的更改并自动尝试合并到当前分支-相当于`git fetch + git merge`
- #### 如果发现主干中有BUG怎么处理
   - 创建一个新的分支进行bug修复:
      - git checkout main, git pull, git checkout fix321
      - git add.  ,  git commit"fix done"
      - git checkout main, git pull, git merge fix321
      - git push origin(远程仓库的本地别名) main
