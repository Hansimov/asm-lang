# 第 13 章：int 指令

## 检测点 13.1

（1）在上面的内容中，我们用 7ch 中断例程实现 loop 的功能，则上面的 7ch 中断例程所能进行的最大转移位移是多少？

```text
lp:     push bp
        mov bp,sp
        dec cx
        jcxz lpret
        add [bp+2],bx
lpret:  pop bp
        iret
```

答案：最大转移位移是指的相对地址的偏移量，它用十进制表示比较好。

如果从 loop 的指令角度上看，loop 就是个段内短转移，它的范围是 -128~127，也就是说最大转移位移是 128。

如果从例程中 bx 变量来说，先搞定 bx 存储二进制补码数据的表示范围。对于 16 位来说它表示的范围是（-32768~32767）。我们知道在内存中是按照计算机补码方式存储的，最小的负数（也就是绝对值最大的数）是 8000H 也就是 -32768，那么它最大的转移位移是 32768（绝对值）。

（2）用 7ch 中断例程完成 **jmp near ptr s** 指令的功能，用 bx 向中断例程传送转移位移。

应用举例：在屏幕的笫 12 行，显示 data 段中以 0 结尾的宇符串 。

```text
assume cs:code

data segment
    db 'conversation',0
data ends

code segment
start:  mov ax,data
        mov ds,ax
        mov si,0
        mov ax,0b800h
        mov es,ax
        mov di,12*160
s:      cmp byte ptr [si],0
        je ok                       ; 如果是 0 跳出循环
        mov al,[si]
        mov es:[di],al
        inc si
        add di,2
        mov bx,offset s-offset ok   ; 设置从标号 ok 到标号 s 的转移位移
        int 7ch                     ; 转移到标号 s 处
ok:     mov ax,4c00h
        int 21h
code ends

end start
```

此程序目的是将 int 7cH 指令等价于 **jmp near ptr s**（段内近转移），位移范围是（-32768~32767）。

7cH 的入口参数是 bx，实现功能是 **jmp near ptr s**（段内近转移），返回值：无。

例程代码如下：

```text
lp:
    push bp         ; 将 bp 这个 ss 栈的偏址保存
    mov bp, sp      ; 将当前栈顶指针值送入到 bp
    add [bp+2], bx  ; 修改 ss 栈中的从栈顶向下第 2 个单元的值
lpret:
    pop bp          ; 恢复 bp 值
    iret            ; 返回到调用处
```

将 7cH 代码写入 0:200H 的装载程序如下：

```text
assume cs:code

code segment
start:      ; 7cH 中断例程的安装程序
            mov ax, cs
            mov ds, ax
            mov si, offset lp   ; 将 ds:si 指向源地址（captial 的机器码）
            mov ax, 0000H
            mov es, ax
            mov di, 200H        ; 将 es:di 指向目的地址（0:200H 向量表中）
            mov cx, offset lpend - offset lp    ; 设置传输长度
            cld             ; 传输方向为正
            rep movsb       ; 字节传输
            ; 设置中断向量表，使 7cH 条目中断向量指向 0000:200H
            mov ax, 0000H
            mov es, ax
            mov word ptr es:[7cH*4], 200H
            mov word ptr es:[7cH*4+2], 0000H
            mov ax, 4c00H
            int 21H
;-----------
; 装载的例程：7cH
; 功能：int 7cH 实现和 jmp near ptr s 指令相同的功能
; 入口参数：bx 相对地址偏移量
; 返回值：无
;-----------
lp:
    push bp         ; 将 bp 这个 ss 栈的偏址保存
    mov bp, sp      ; 将当前栈顶指针值送入到 bp
    add [bp+2], bx  ; 修改 ss 栈中的从栈顶向下第 2 个单元的值
lpret:
    pop bp          ; 恢复 bp 值
    iret            ; 返回到调用处。
lpend:
    nop             ; 代码段结尾，便于计算 7cH 例程的长度。   

code ends

end start
```

## 参考链接

* 汇编语言（王爽第三版）检测点13 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6883959.html](https://www.cnblogs.com/Base-Of-Practice/articles/6883959.html)

