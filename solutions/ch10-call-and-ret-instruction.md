# 第 10 章：CALL 和 RET 指令

## 检测点 10.1

补全程序 ，实现从内存 1000:0000 处开始执行指令。

```text
assume cs:code

stack segment
    db 16 dup (0)
stack ends

code segment
start:
    mov ax,stack
    mov ss,ax
    mov sp,16
    mov ax,1000h    ; CS，先入后出
    push ax
    mov ax,0000h    ; IP
    push ax
    retf
code ends

end start
```

