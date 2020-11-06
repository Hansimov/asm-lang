# 第 15 章：外中断

## 15.4 编写 int 9 中断例程（注释版）

```text
; 在屏幕中间依次显示 “a" ~ "z", 并可以让人看清。
; 在显示的过程中，按下 Esc 键后，改变显示的颜色。

assume cs:code
stack segment
    db 128 dup (0)
stack ends

data segment
    dw 0,0
data ends

code segment
start:
    mov ax,stack
    mov ss,ax       ; ss 指向 stack 空间
    mov sp,128      ; 栈顶，栈一共有 128 字节

    mov ax,data
    mov ds,ax       ; ds 指向 data 空间

    mov ax,0
    mov es,ax       ; es 指向中断向量表，位于内存地址 0 处
                    ; 0:0000 ~ 0:03FF 的 1024 个单元存放中断向量表，一个表项 2 个字

    push es:[9*4]   ; 暂存原 int 9 中断例程的入口地址
    pop ds:[0]      ; 暂存位置为 ds:[0]、ds:[2] 处
    push es:[9*4+2]
    pop ds:[2]      

    mov word ptr es:[9*4], offset int9
    mov es:[9*4+2],cs   ; 在中断向量表中设置新的 int9 中断例程的入口段地址和偏移地址，其中段地址等于当前 cs

    mov ax,0b800h
    mov es,ax
    mov ah,'a'              ; 从字符 'a' 开始显示
s:  
    mov es:[160*12+40*2],ah
    call delay
    inc ah
    cmp ah,'z'
    jna s

    mov ax,0
    mov es,ax               ; es 指向中断向量表，内存地址 0 处

    push ds:[0]     ; 从 ds[0]、ds[2] 处取出原 int 9 入口地址
    pop es:[9*4]    ; 将中断向量表中 int 9 的入口地址恢复为原地址
    push ds:[2]
    pop es:[9*4+2]
                        
    mov ax,4c00h
    int 21h

delay:
    push ax
    push dx
    mov dx,2000h    ; 空运行 2000,0000h 次
    mov ax,0
s1:
    sub ax,1
    sbb dx,0
    cmp ax,0
    jne s1
    cmp dx,0
    jne s1
    pop dx
    pop ax
    ret
int9:
    push ax
    push bx
    push es

    in  al,60h      ; 读取键盘输入

    ; 模拟 int 过程
    pushf
    pushf
    pop bx
    and bh,11111100b    ; 将 IF 和 TF 置 0
    push bx
    popf
    call dword ptr ds:[0] ; 调用原 int 9 中断例程
    
    cmp al,1            ; esc 扫描码为 1，q 键扫描码为 01h
    jne int9ret

    mov ax, 0b800h
    mov es,ax
    inc byte ptr es:[160*12+40*2+1] ; 修改字符属性

int9ret:
    pop es
    pop bx
    pop ax
    iret

code ends
end start
```

## 检测点 15.1

（1）仔细分析一下上面的 int 9 中断例程，看看是否可以精简一下？

其实在我们的 int 9 中断例程中，模拟 int 指令调用原 int 9 中断例程的程序段是可以精简的，因为在进入中断例程后， IF 和 TF 都已经置 o, 没有必要再进行设置了 。 对于程序段：

```text
pushf
pushf
pop ax
and ah,11111100b
push ax
popf
call dword ptr ds:[0]
```

可以精简为：

```text
pushf
call dword ptr ds:[0]
```

两条指令。

**进一步解析：**

1、调用任何中断处理例程，CPU 都执行如下流程： 

1. 取得中断类型码 n
2. 标志寄存器入栈保存（因为在中断过程中要改变标志寄存器的值）
3. 设置标志寄存器的第 8 位 TF（跟踪标志）和第 9 位 IF（中断标志）为 0（防止单步中断和其他外部中断发生）
4. CS、IP 入栈
5. \(IP\)=\(n\*4\), \(CS\)=\(n\*4+2\) 
6. 执行中断例程

所以第二个 pushf 是多余的（第 2 步已经入栈），设置 IF 和 TF 的指令也是多余的。

2、为什么还有个 pushf 呢，这个 pushf 指令压栈标志寄存器，确实是保护标志寄存器的值，也是为了与中断程序中的 iret（它内部 CPU 操作步骤有 popf）相呼应。如果没有这个 pushf，那么 iret 指令执行中出栈到标准寄存器的值可能不正确了。

（2）仔细分析上面程序中的主程序，看看有什么潜在的问题？

在主程序中，如果在执行设置 int 9 中断例程的段地址和偏移地址的指令之间发生了键盘中断，则 CPU 将转去一个错误的地址执行，将发生错误。

找出这样的程序段，改写它们，排除潜在的问题。

提示，注意 sti 和 cli 指令的用法。

**解答：**

设置新中断向量表代码：

```text
mov ax,0
mov es,ax           ; es 指向中断向量表，内存地址 0 处

mov word ptr es:[9*4], offset int9
mov es:[9*4+2],cs   ; 在中断向量表中设置新的 int9 中断例程的入口段地址和偏移地址
                    ; 其中段地址等于当前 cs
```

写回原中断向量表：

```text
mov ax,0
mov es,ax       ; es 指向中断向量表，内存地址 0 处

push ds:[0]     ; 从 ds[0]、ds[2] 处取出原 int 9 入口地址
pop es:[9*4]    ; 将中断向量表中 int 9 的入口地址恢复为原地址
push ds:[2]
pop es:[9*4+2]
```

如果在指令执行在这两端程序中间，触发键盘中断事件，那么由于正在设置中断向量表，\(ip\) 和 \(cs\) 值可能不确定。为了避免这 2 段代码不受中断事件干扰，需要将中断屏蔽。在这两段代码前后加上 cli 和 sti 指令即可。

```text
cli ; 屏蔽任何（可屏蔽）中断
.
.
.
sti ; 解除屏蔽（可屏蔽）中断
```

### 参考链接

* 汇编语言（王爽第三版）检测点15 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6883993.html](https://www.cnblogs.com/Base-Of-Practice/articles/6883993.html)

## 实验 15 安装新的 int 9 中断例程

安装一个新的 int 9 中断例程，功能：在 DOS 下，按下 “A” 键后，除非不再松开，如果松开，就显示满屏幕的 “A”，其他的键照常处理。

提示，按下一个键时产生的扫描码称为通码，松开一个键产生的扫描码称为断码。断码=通码+80h 。

```text

```

**解析：**

1、不像前面章节中的中断例程，安装完毕后可以测试下。这个程序依然不能测试。自己体会吧。只有安装了 DOS 系统下才能测试。

2、临时存储原来 int9 中断例程的中断向量的空间在代码段中的 \[200H\] ~ \[203H\] 中，我们一般不在代码段中存储数据。代码段只是存储 CPU 执行的代码。

3、此程序可谓是一体的，测试程序和安装程序都在一个程序段内，没有了原来上面章节中介绍的例程装载程序。将中断例程直接写到了 code 段中。

4、在命令提示符窗口中，可以运行此程序，但不能使用 int 9 中断程序。也就是说在程序中从 a 到 z 变化过程中，我们不能按任何键（会调用中断 int9 的，而且是新的中断例程），由于中此程序中断向量表不能正常更新，故 int9 程序调用时出错。

5、理解这个程序吧，除非我们处在 DOS 环境中。其实玩汇编就是在 DOS 下的，写写操作系统啦，编个程序啦。

### 参考链接

* 汇编语言（王爽第三版）实验15 安装新的int 9中断例程 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6883998.html](https://www.cnblogs.com/Base-Of-Practice/articles/6883998.html)

