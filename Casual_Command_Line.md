# 常用指令
## `gcc/g++`
* 预处理,生成.i的文件`[使用-E参数]`

* 将预处理后的文件转换成汇编语言,生成文件.s`[使用-S参数]`

* 由汇编变为目标代码(机器代码)生成.o的文件`[使用-c参数]`

* 连接目标代码,生成可执行程序`[使用-o参数]`
* 生成可调试文件a.out `[使用-g参数]`
## `objdump`
* 反编译所有节`[--disassemble-all]`
* 选择intel语法 `[-M intel]`
* 输出到文件 `[ > disassemble.txt]`
## `gdb`
* 装可执行文件(.out/.exec等) `[file filename]`
* 运行 `[run]`
* 查看信息  `[info]`或其缩写`[i]`
* 打断点 `[break func_name/line_num/* instruction_addr]`
* 连接到正在运行的进程 `[attach pid]`
* 查看调用栈 `[bt]`
* 反编译(可以用以查看装载后指令在内存中的地址) `[disassemble func_name]`
* 跳出循环
* 跳出函数 `[return]`
* 执行下一条语句(进入函数)`[s]`
* 执行下一条语句(不进入函数) `[n]`