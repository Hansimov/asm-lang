# 第 9 章：转移指令的原理

## 检测点 9.1

（1）程序如下。若要使程序中的 jmp 指令执行后， CS:IP 指向程序的笫一条指令，在 data 段中应该定义哪些数据？

```text
assume cs:code

data segment
    ?
data ends

code segment
start:
    mov ax,data
    mov ds,ax
    mov bx,0
    jmp word ptr [bx+1]
code ends

end start
```

答案：**word ptr \[bx+1\]** 指的是一个字单元，也即 2 个字节，也就是说在 data 段中是第 2~3 字节，如果 jmp 转移到程序第一条指令，也即 IP 的值为 0000 即可。故只需第 2、3 字节均为 0 即可。如下：

```text
data segment
    dw 3 dup (0)
data ends
```

（2）程序如下。补全程序，使 jmp 指令执行后， CS:IP 指向程序的笫一条指令 。

```text
assume cs:code

data segment
    dd 12345678H
data ends

code segment
start:
    mov ax,data
    mov ds,ax
    mov bx,0
    mov [bx],_____
    mov [bx+2],_____
    jmp dword ptr ds:[0]
code ends

end start
```

答案：\[bx\] 代表低 16 位的字，对应 IP；\[bx+2\] 代表了高 16 位的字，即 CS。所以只需要保证 data 段中第 1、2 字节是 IP 的值，第 3、4 字节是 CS 的值即可。

```text
    mov [bx],offset start  ; IP
    mov [bx+2],code        ; CS
```

（3）用 Debug 查看内存，结果如下：

```text
2000:1000    BE 00 06 00 00 00 ...
```

则此时，CPU 执行下述命令后，\(CS\)=?，\(IP\)=?：

```text
mov ax,2000H
mov es,ax
jmp dword ptr es:[1000H]
```

答案：\(CS\)=0006h，\(IP\)=00BEh。

## 检测点 9.2

补全编程，利用 jcxz 指令，实现在内存 2000H 段中查找笫一个值为 0 的字节，找到后，将它的偏移地址存储在 dx 中 。

```text
assume cs:code

code segment
start:
    mov ax,2000H
    mov ds,ax
    mov bx,0
s:
    mov ch,0       ;
    mov cl,[bx]    ;
    jcxz ok        ;
    inc bx         ;
    jmp short s
ok:
    mov dx,bx
    mov ax,4c00h
    int 21h
code ends

end start
```

答案：这里我们需要 jcxz 实现循环的跳出，需要想办法将 cx 的内容赋值为 ds:bx，也就是说从 ds:\[0\] 开始，逐个字节的将单元内容赋值给 cx，然后执行 jcxz 语句。

由于是逐个字节的比较，bx 的偏移量应以字节为单元。我们使用的 cx 寄存器是 16 位的，因此只需要低 8 位的 cl 寄存器就可以了。为了保证 ch 为 0，首先必须置零。它们组合在一起就是 cx 的整体值。

### 参考链接

* 汇编语言（王爽第三版）检测点:9 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6883918.html](https://www.cnblogs.com/Base-Of-Practice/articles/6883918.html)

