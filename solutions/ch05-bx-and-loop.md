# 第 5 章：\[BX\]和 loop 指令

## 问题 5.1

| 指令 | ax | bx | ds | 内存 |
| :--- | :---: | :---: | :---: | :---: |
| mov ax,2000H | 2000 | / | / |  |
| mov ds,ax |  | / | 2000 |  |
| mov bx,1000H |  | 1000 |  |  |
| mov ax,\[bx\] | \(21000\) = 00BE |  |  |  |
| inc bx |  | 1001 |  |  |
| inc bx |  | 1002 |  |  |
| mov \[bx\],ax |  |  |  | \(21002\)=00BE |
| inc bx |  | 1003 |  |  |
| inc bx |  | 1004 |  |  |
| mov \[bx\],ax |  |  |  | \(21004\)=00BE |
| inc bx |  | 1005 |  |  |
| mov \[bx\],al |  |  |  | \(21005\)\(byte\)=BE |
| inc bx |  | 1006 |  |  |
| mov \[bx\],al |  |  |  | \(21006\)\(byte\)=BE |

**内存中的情况**

| 地址（Hex） | 值 |
| :---: | :---: |
| 21000 | BE |
| 21001 | 00 |
| 21002 | **BE** |
| 21003 | **00** |
| 21004 | **BE** |
| 21005 | **00-&gt;BE** |
| 21006 | **BE** |
| 21007 |  |

## 实验 4 \[bx\] 和 loop 的使用

（1）编程，向内存 0:200~0:23F 依次传送数据 0~63\(3FH\)。

{% tabs %}
{% tab title="lab4\_1.asm" %}
```text
assume cs:code
code segment
	mov ax,20h
	mov ds,ax

	mov bx,0
	mov cx,64

s:	mov [bx],bx
	inc bx
	loop s

	mov ax,4c00h
	int 21h

code ends
end
```
{% endtab %}
{% endtabs %}

（2）编程，向内存 0:200~0:23F 依次传送数据 0~63\(3FH\)，程序中只能使用 9 条指令， 9 条指令中包括 “mov ax,4c00h” 和 “int 21h”。

答案：同（1）。

（3）下面的程序的功能是将 “mov ax,4c00h " 之前的指令复制到内存 0:200 处，补全程序。上机调试，跟踪运行结果。

```text
assume cs : code
    code segment

    mov ax,cs            ; 程序入口为cs:0(ip=0)，所以第一空处传递段地址为cs
    mov ds,ax
    mov ax,0020h
    mov es,ax
    mov bx,0
    mov cx,0017h         ; 先将第二空位随便设为1，加载后观察 mov ax,4c00h 之前代码
                         ; 为0000~0016h，总长度为23.

s:  mov al,[bx]
    mov es:[bx],al
    inc bx
    loop s

    mov ax,4c00h
    int 21h

code ends
end
```

提示： 

（1）复制的是什么？从哪里到哪里？ 

答案：复制的是存放程序代码的数据。从 XXXX:0h~XXXX:17h

（2）复制的是什么？有多少个字节？你如何知道要复制的字节的数量？

答案：复制的是程序代码的数据。23个字节。先将第2个补全空位随便设为1，编译链接汇编代码后，debug 可执行文件，用 **u** 观察 **mov ax,4c00h** 之前代码（为 0000~0016h，总长度为23）。

### 参考资料

* 汇编语言（王爽第三版）实验4 \[bx\]和loop的使用 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6883892.html](https://www.cnblogs.com/Base-Of-Practice/articles/6883892.html)
* 汇编语言（王爽）实验4 - tsembrace - 博客园 
  * [https://www.cnblogs.com/tsembrace/p/3251774.html](https://www.cnblogs.com/tsembrace/p/3251774.html)

