# `CRASH pwn02`

## SOLUTION

### `1、覆盖返回地址`

汇编

```
1.text:08049194 mov     eax, ds:dword_80EBF9C
2.text:08049199 lea     edx, [eax+1]
3.text:0804919C mov     ds:dword_80EBF9C, edx
4.text:080491A2 mov     edx, [ebp+result]
5.text:080491A5 mov     [ebp+eax*4-34h], edx
6.text:080491A9 jmp     loc_80488D4
```

反编译伪代码

```
count = dword_80EBF9C++;
v6[count] = result;
```

关键点在于全局变量dword_80EBF9C会在每次读并输出后+1。而v6是个局部变量，存在栈中，当全局变量dword_80EBF9C足够大时，在执行第5条指令时，偏移量足够让result覆盖掉栈中保存的返回地址。
</br>但是注意第6条仍是一个jmp指令，我们仍处于读并输出的循环中，而要出发程序崩溃不仅需要覆盖返回地址，还要执行返回！

### `2、设法执行返回`

返回条件
汇编
```
.text:08049178 mov     [ebp+result], eax
...
.text:0804918E cmp     [ebp+result], 0
.text:08049192 jz      short loc_80491AE
```
即需要eax==0。
</br>而eax是
```
.text:080488E4 call    sub_804929E     ; 获取输入
.text:080488E9 mov     [ebp+result], eax
.text:080488EC mov     eax, [ebp+result]
.text:080488EF add     eax, 35C1A629h
.text:080488F4 xor     eax, 0A2E159D4h
...很多个xor和sub
.text:08049169 sub     eax, 71D68C0Dh
.text:0804916E xor     eax, 1977702Ch
.text:08049173 sub     eax, 2E72824Dh
```

即eax是函数sub_804929E（获取输入的函数）返回值经过若干xor和sub计算后的值，而xor和sub又是可逆的，由此可以逆推出eax=0时的输入值应该是多少。
</br>最终解决方案为先输入足够多的数据让返回地址被覆盖，再输入能让eax=0的值，让函数执行ret返回从而崩溃。

## 思路
栈漏洞可以看哪里往栈里存了数据，有没有大小、偏移量限制
## 总结