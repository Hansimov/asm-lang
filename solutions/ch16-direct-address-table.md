# 第 16 章：直接定址表

## 检测点 16.1

下面的程序将 code 段中 a 处的 8 个数据累加，结果存储到 b 处的双宇中，补全程序。

```text
assume cs:code
code segment
    a dw 1,2,3,4,5,6,7,8    ; code:[0] ~ code:[15]
    b dd 0                  ; code:[16]~ code:[19]
start:
    mov si,0
    mov cx,8
s:  mov ax,a[si]            ; 将 a 中偏移量为 si 的内存单元按字送入 ax
    add word ptr b[0],ax    ; 与 bx 的低字相加
    adc word ptr b[2],0     ; 与 bx 的高字相加
    add si,2                ; ax 单位为字，因此偏移 2 个字节，偏移量的单位是字节
    loop s

    mov ax,4c00h
    int 21h
code ends
end start
```

## 检测 16.2

下面的程序将 data 段中 a 处的 8 个数据累加，结果存储到 b 处的字中，补全程序 。

```text
assume cs:code,ds:data

data segment
    a db 1,2,3,4,5,6,7,8
    b dw 0
data ends

code segment
start:
    mov ax,data     ; 
    mov ds,ax       ; 
    mov si,0
    mov cx,8
s:
    mov al,a[si]
    mov ah,0
    add b,ax
    inc si
    loop s

    mov ax,4c00h
    int 21h
code ends

end start
```

### 参考链接

* 汇编语言（王爽第三版）检测点16 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6884001.html](https://www.cnblogs.com/Base-Of-Practice/articles/6884001.html)

