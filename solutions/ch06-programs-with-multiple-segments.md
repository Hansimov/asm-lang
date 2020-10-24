# 第 6 章：包含多个段的程序

## 检测点 6.1

（1）下面的程序实现依次用内存 0:0~0:15 单元中的内容改写程序中的数据 ， 完成程序：

```text
assume cs:codesg
codesg segment

        dw  0123h,0456h,0789h,0abch,0defh,0fedh,0cbah,0987h

start:  mov ax,0
        mov ds,ax
        mov bx,0

        mov cx,8
    s:  mov ax,[bx]
        mov cs:[bx],ax  ; Add codes here
        add bx,2
        loop s
        
        mov ax,4c00h
        int 21h

codesg ends
end start
```

（2）下面的程序实现依次用内存 0:0~0:15 单元中的内容改写程序中的数据，数据的传送用栈来进行。栈空间设置在程序内。完成程序：

```text
assume cs:codesg
codesg segment
        
        dw 0123h,0456h,0789h,0abch,0defh,0fedh,0cbah,s0987h
        dw 0,0,0,0,0,0,0,0,0,0

start:  mov ax,cs       ; stack segment address should be 
                        ;   the same with cs, because the space of stack
                        ;   is allocated in code segments
        mov ss,ax
        mov sp,24h      ; Initial address of stack bottom,
                        ;   09h(16 dw) + 14h(10 dw 0) + 1 = 24h
        mov ax,0
        mov ds,ax
        mov bx,0
        mov cx,8

    s:  push [bx]       ; push ds:[bx] to stack top
        pop ss:[bx]     ; pop stack top to memory cs:[bx]
        add bx,2
        loop s
        
        mov ax,4c00h
        int 21h
    
codesg ends
end start
```



### 参考链接

* 汇编语言（王爽第三版）检测点6.1 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6883898.html](https://www.cnblogs.com/Base-Of-Practice/articles/6883898.html)



