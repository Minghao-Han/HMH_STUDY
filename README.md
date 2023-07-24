# HMH_STUDY
## 2023.7.13
complete git config on docker-ubuntu, successfully access to remote git repository 11:08
learning to read assambly code
## 2023.7.17
完成linux中elf格式和x86汇编和指令集的学习
学会IDA和gdb的基本使用
## 2023.7.18
* `.text节、.data节等是什么意思?`

    .text节 elf文件中的程序代码所在的节，可读可执行但不可写
    .data节 elf文件中已初始化的程序全局变量或静态变量所在的节，可读写
* `在一个典型的C/C++程序中，全局变量、栈变量、堆变量，在汇编语言层面是如何体现的?`

    * 全局变量：已初始化的存放在.data，未初始化的存放在.bss，在代码段被使用。
    * 堆变量：调用new,malloc等库函数,再由库函数调用system call在内存上分配空间，参数为需要分配的字节数(编译器自行计算)。且必须销毁！（会先检查是否为空指针，若不是才进行销毁）

        *注意：int* ptr = new int[5];中的ptr是局部变量，栈变量，new出来的数组才是放在堆里的。
    * 栈变量：程序中的局部变量或部分函数参数，存放在栈中。每个函数都自己的帧，由栈底指针确定当前栈。以小端系统为例，栈顶指针增回收栈，减扩容栈，push压栈并减，pop弹栈并增。一般通过栈底指针加偏移量取值(栈底指针较稳定)。在x86-64中栈底指针通常存放在rbp，栈顶指针存放在rsp。(risc-v的fp,sp)。
    </br><font color=grey>*注意：一个函数中的所有局部变量(non-static)都会在函数一开始为他们在栈上分配空间*</font>
* `ELF程序的节的权限是什么意思，例如，什么是可读、可写、可执行等?`

    节的权限是指在程序运行时对节的操作权限。

    可读，通常的elf所有节都是可读的，但也可以手动更改设为不可读。
    可写，如.bss节, .data节，可以对节的内容进行修改
    可执行，如.text节(程序的代码段)。
* `ELF程序中，符号是什么意思，调试信息是什么意思，字符串、符号、调试信息有什么区别?`

    * 符号（Symbols）是与程序中的函数、变量或其他实体相关联的标识符。
        * 有三种类型（c/c++为例）:
            * global symbol 没被static修饰的全局变量，外部文件可引用
            * external symbol 引用的定义在别的文件中的global symbol
            * local symbol 被static修饰的全局变量，只在本文件中可被引用

        * 强弱之分：已初始化的为强符号，反之为弱。对符号引用和符号定义连接的过程会有影响：
            * 不能有同名的强符号，会error
            * 同名的强符号和弱符号，linker选强符号
            * 同名的弱符号，linker随机选择一个  

    * 调试信息是是与程序执行相关的附加数据，用于在调试和分析程序时提供更多的上下文和信息。调试信息包含有关源代码文件、行号、变量名称、类型信息和编译器生成的其他调试相关信息。这些信息有助于调试器和其他工具在源代码级别进行调试和分析。gcc/g++中带参数-g生成可调试的可执行文件.out
    * 字符串是一种数据类型，用于存储文本数据，通常用于表示符号名称、文件路径和调试信息中的文本

* `x86-64寄存器`

    常用的有：
    * `rax` 常用作函数返回值
    * `rsp`栈顶指针
    * `rbp` 栈底指针
    * `rdi,rsi,rdx,rcx` 常用作入参数
    * `rip` 指令指针寄存器
    * `r8~r15` 通用寄存器
    * `rflags` 标识寄存器
        </br>该寄存器中有许多位，分别作为不同的标志。常用的有
        CF： 进位标志，PF ： 奇偶标志，ZF ： 零标志，SF ： 符号标志，OF ： 补码溢出标志，TF： 跟踪标志，IF ： 中断标志

    </br>
* `常见寻址模式在汇编语言中的表现形式`
    * 直接寻址 `mov rax, QWORD PTR [0x007]`
    * 立即数寻址 `mov rdi,0x0`
        </br>$\quad$ 常见于：
        * 局部变量赋值
            ``` c++
            int a = 0; 
            ```
        * for,if的数值大小判断
            ``` c++
            if (c<10)
            ```
        * 栈的扩容和回收
            ``` amd64
            sub rsp,32
            ```
    * 寄存器寻址 `mov rdi,rax`
    * 寄存器相对寻址 `mov DWORD PTR -16[rbp],edi`
        </br>$\quad$ 常见于：
        * 局部变量的赋值和引用（即使用栈）
            ```  c++
            int a=5; 
            a=c;
            delete ptr;
            ```
            * 访问结构体或类对象的成员
            ```  c++
            Human jack;
            jack.id =1;
            ```
    * rip-relative `lea	rax, .LC0[rip]` .LC0是字符串相对于rip的偏移量，是PIC
    </br>$\quad$ 常见于：
        * 对全局变量的使用
        * 字符串常量的使用
            ``` c++
            string str = "hello world";
            printf("cycle times %d",index);
            ```
    * <font color=#ff0>以上各种寻址可总结为</font>
        ```
        EffectiveAddress = BaseReg + IndexReg * ScaleFactor + Disp
        ```
    </br>
* `条件跳转、非条件跳转、间接跳转、函数调用、返回`
    </br>*<font color=#FF808>注意：跳转指令以及call操作的立即数就是相对于rip(或esp)的偏移，不是绝对地址 !</font>*
    * 条件跳转 检查标识位寄存器rflags
    </br>JE/JZ：`[ZF==1]`，则跳转到目标地址。
    </br>JNE/JNZ：`[ZF==0]`，则跳转到目标地址。
    </br>JG/JNLE：`[ZF==0 && SF==OF]`，则跳转到目标地址。
    </br>JGE/JNL：`[ZF==1 || SF!=OF]`，则跳转到目标地址。
    * 非条件跳转
    </br>JMP 直接跳转到目标地址位置
    * 间接跳转 常见于调用其他模块的函数，如使用标准c库的printf()。先跳转plt，在从got中找到并跳转该函数第一条指令的实际内存地址处。
    * 函数调用
    </br>call 跳转并将rip的值（即下一条指令的地址）压栈
    * 返回
    </br> ret 从栈中弹出返回地址并跳转，同时恢复栈底指针rbp到上一函数帧的栈底。
* `什么是PIE、PIC?`
    * 什么是Position-Independent?
            </br>$\quad$ 位置无关，无论文件装载到内存的哪个位置，指令的二进制码都不会改变。
    * PIE(Position-Independent Executable) 位置无关代码所组成的可执行二进制文件，有时可称为PIC Executable。当程序加载时，PIE文件以及它所有的依赖都会加载到虚拟内存空间中的随机位置
    * PIC(Position-Independent Code)位置无关代码。典型的如对共享库的调用。通过plt,got进行跳转,指令中没有实际的函数地址，在运行时由动态链接器确定并写入.got。具体可参考[->Linux 动态链接过程中的【重定位】底层原理](https://mp.weixin.qq.com/s/5oK1-uO_7d3bjN8IXXw8EQ)
    * 位置无关的实现：
        * 跳转指令 立即数即相对于rip(或esp)的偏移
        * 字符串常量等rodata以及全局变量.bss/.data的使用 通过rip相对寻址
        * 对动态链接库的使用
* `string instruction`
    * 专用于字符串操作的指令，有cmps,movs,lods,stos等
    * 前缀指令 REP、REPE与REPNE:
        * rep 重复执行，终止条件为`[rcx==0]`。常用于字符串的复制
            ```
            rep movsb
            ```
            从源地址(RSI)复制n个字节到目标地址(DI)
        * repe/repz
            rep 重复执行，终止条件为`[rcx==0 && ZF=0]`。常用于字符串的比较
            ```
            repe cmps
            ```
            找出源地址(RSI)和目标地址(RDI)之间不一样的字
        * repnz/repne
            即repe/repz取反
* `函数调用时的参数传递方案（即AMD64 Calling Convention）`
    * <font color=#099>calling convention的定义以及意义：</font></br>
    *' calling convention '是规定子过程如何获取参数以及如何返回的方案。它明确了主调函数和被调函数在参数、返回值、栈底指针、返回地址等数据的保存、恢复、使用、销毁上的责任归属，确保程序能够正常的调用和返回。*
    </br></br>
    1. 入参 调用前需要为被调函数准备好参数
        * 根据需要取用rdi,rsi,rcx,rdx,r8,r9寄存器。首先将寄存器中的原本的值压栈，再将参数的值给寄存器。
        * 若入参个数大于六，则从第五个入参开始都存在栈里，则存放于栈中。
    2. call 指令调用被调函数，同时将返回地址（即下一条指令的地址）压栈。
    3. 被调函数
        * 将主调函数的rbp压栈，然后修改rbp使其指向被调函数帧的栈底（mov rbp,rsp）
        * 通过寄存器和栈底指针rbp+offset取入参。
    4. 返回
        * 返回值存在rax寄存器
        * ret 从栈中弹出返回地址并跳转，同时恢复栈底指针rbp到上一函数帧的栈底。
        * 清理栈中的参数。
        * 从栈中恢复rsi,rdi,rcx,rdx,r8,r9寄存器（若使用）。
## 2023.7.20
* ready to make some exec crash
