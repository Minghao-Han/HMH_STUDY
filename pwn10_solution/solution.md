# `CRASH pwn10`

## SOLUTION
该题同样是一个栈溢出漏洞

### `函数功能`

```
读取数字->和10比较
          |
          |__小于-->和读取字符串->生成一个奇怪字符串->return
          |
          |__大于等于-->输出"wrong key"->...(不重要)
```

### `返回`

输入的数字<10即可让该函数执行到返回

### `覆盖返回地址`

对字符串的读取输入都没有判断栈变量的容量，很容易实现溢出

### `总结`

如何判断读取输入有没有判断栈变量的容量

``` c
if(是通过循环一个一个读取输入的字符){
  if(没有设置最大循环次数) 危险!
}else if(通过调用别的函数读，栈变量的地址作为一个参数){
  if(没有向被调函数传递栈变量的大小) 危险！
}else{
  unknown
}
```
如此题中,调用sub_804F840(一个读输入函数)时没有将char[]的大小作为一个参数，则用户可以输入任意长的字符串并被写入栈，只要足够长就可以覆盖掉函数的返回地址

``` assembly
sub     esp, 0Ch
lea     eax, [ebp+var_8C]
push    eax
call    sub_804F840     ; 获取输入
```
