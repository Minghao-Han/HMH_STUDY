# 常用指令

## `Linux`

* 显示进程虚拟内存位置`[cat /proc/[进程的 pid]/maps]`

## `gcc/g++`

* 预处理,生成.i的文件`[使用-E参数]`

* 将预处理后的文件转换成汇编语言,生成文件.s`[使用-S参数]`

* 由汇编变为目标代码(机器代码)生成.o的文件`[使用-c参数]`

* 连接目标代码,生成可执行程序`[g++ file1.o [file2.o ...] -o all_file]`
    * 不开启堆栈溢出保护，即不生成 canary `[-fno-stack-protector]`
* 生成可调试文件a.out `[使用-g参数]`
    * 指定intel语法 `[-masm=intel]`

## `objdump`

* 反编译所有节（不包含非空）`[--disassemble-all]`
* 选择intel语法 `[-M intel]`
* 输出到文件 `[ > disassemble.txt]`

## `gdb`

* 装可执行.out文件 `[file filename.out]`
* 装可执行.exec文件(.out/.exec等) `[exec-file filename]`
* 运行 `[run]`或缩写 `[r]`
* 查看信息  `[info]`或其缩写`[i]`
* 查看所断点`[info breakpoints]` 或缩写 `[i b]`
* 打断点 `[break func_name/line_num/* instruction_addr]`
* 连接到正在运行的进程 `[attach pid]`
* 查看调用栈 `[bt]`
* 反编译(可以用以查看装载后指令在内存中的地址) `[disassemble func_name]`
* 跳出循环
* 跳出函数 `[return]`
* 执行下一条语句(进入函数)`[s]`
* 执行下一条语句(不进入函数) `[n]`

## `git`

* 添加远程仓库 `[remote add origin git_repo_addr]`
* pull,push,clone,fetch
* 切换分支`[checkout branch_name]`

## `docker`

* 查看所有镜像
* 运行一个镜像（启动容器）`[run image_id]`或`[run image_name:version]`
    </br>*可缩写到一起，如`[-itv]`*
    * `[-i]`
    * `[-t]`
    * 挂卷 `[-v 本机文件夹地址:容器挂载地址]`
    * `[--name container_name]`
* 在容器中执行 `[exec 命令行指令]`
* commit `[docker   commit 容器id 目标镜像名:[TAG]]`
    * `[-m="描述信息"]` 
    * `[-a="作者"]`
]`
* 卷 `[volume]`

## `checksec`
* 用来检查可执行文件属性，例如PIE, RELRO, PaX, Canaries, ASLR, Fortify Source等等属性
* 查看 `[checksec filename]`