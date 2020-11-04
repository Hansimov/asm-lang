# 第 11 章：标志寄存器

## 检测点 11.1

写出下面每条指令执行后，ZF、PF、SF 等标志位的值。

| 指令 | ax | bx | ZF | PF | SF |
| :--- | :--- | :--- | :---: | :---: | :---: |
| sub al,al | 0000 |  | 1 | 1 | 0 |
| mov al,1 | 0001 |  | - | - | - |
| push ax | \(0001\) |  | - | - | - |
| pop bx |  | 0001 |  | - | - |
| add al,bl | 0002 |  | 0 | 0 | - |
| add al,10 | 0012 |  | - | 1 | - |
| mul al | 0144 |  | - | - | - |

## 检测点 11.2

| 指令 | ax | CF | OF | SF | ZF | PF |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| sub al,al | 0000 | 0 | 0 | 0 | 1 | 1 |
| mov al,10H | 0010 | - | - | - | - | - |
| add al,90H | 00a0 | - | - | 1 | 0 | - |
| mov al,80H | 0080 | - | - | - | - | - |
| add al,80H | 0000 | 1 | 1 | 0 | 1 | - |
| mov al,0FCH | 00fc | - | - | - | - | - |
| add al,05H | 0001 | 1 | **0** | - | 0 | - |
| mov al,7DH | 007d | - | - | - | - | - |
| add al,0BH | 0088 | 0 | 1 | 1 | - | 1 |

* CF：直接对十六进制**（无符号数）**的操作数进行运算，得出结果，如果有进位或借位，CF=1
* OF：将操作数转换成十进制数**（有符号数）**，然后运算，得出十进制结果，**看是否在寄存器取值范围内**，如果超出：OF=1；
* SF：执行指令后的结果，最高位是1，SF就是1（负数）；最高位是0，SF就是0（正数）；
* PF：数 1 的个数。或将二进制的所有1相加，得出的十进制结果是偶数，PF=1，否则 PF=0.

## 检测点 11.3

（1）补全下面的程序，统计 F000:0 处 32 个宇节中 ， 大小在 \[32,128\] 的数据的个数 。

```text
mov ax,0f000h
mov ds,ax
mov bx,0
mov dx,0
mov cx,32
s:  mov al,[bx]
    cmp al,32
    jb s0      ; al < 32 
    cmp al,128
    ja s0      ; al > 128 
    inc dx
s0: inc bx
    loop s
```

（2）补全下面的程序，统计 F000:0 处 32 个宇节中 ， 大小在 \(32,128\) 的数据的个数 。

```text
mov ax,0f000h
mov ds,ax
mov bx,0
mov dx,0
mov cx,32
s:  mov al,[bx]
    cmp al,32
    jna s0       ; al <= 32
    cmp al,128
    jnb s0       ; al >= 128
    inc dx
s0: inc bx
    loop s
```

### 参考链接

* 汇编语言（王爽第三版）检测点11 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6883947.html](https://www.cnblogs.com/Base-Of-Practice/articles/6883947.html)



