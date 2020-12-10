1. 查看CPU信息

    `cat /proc/cpuinfo`

2. 查看硬盘使用情况

    `df -ah`

3. 查看内存信息

    `cat /proc/meminfo`

    `free -m/k/g`命令

4. 查看系统版本

    `uname -r`：显示系统内核版本

    `uname -a`：输出所有系统版本信息

5. `grep`过滤命令

    `grep '过滤内容' 文件`，此时区分大小写，

    - 加上-i选项，不区分大小写
    - 加上-n选项，显示行号过滤
    - 加上-E选项，`grep -E "linux|LINUX" 文件`，过滤出带linux和LINUX内容的行

    `cat /etc/passwd|grep "root"`