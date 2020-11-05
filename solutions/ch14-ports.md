# 第 14 章：端口

## 检测点 14.1

（1）编程，读取 CMOS RAM 的 2 号单元的内容。 

```text
assume cs:code

code segment
start:
    mov al,2    ; (al)=2，2 号单元
    out 70h,al  ; 选中端口 70h 的 2 号单元
    in  al,71h  ; 从端口 71h 读出 2 号单元，送入 al

    mov ax,4c00h
    int 21h
code ends

end start
```

（2）编程，向 CMOS RAM 的 2 号单元写入 0 。

```text
assume cs:code

code segment
start:
    mov al,2    ; (al) = 2，2 号单元
    out 70h,al  ; 将 al 送入端口 70h，选中 2 号单元
    mov al,0    ; (al) = 0，写入端口
    out 71h,al  ; 将 (al)=0 写入到 71h 的 2 号单元
    mov ax,4c00h
    int 21h
code ends

end start
```

**使用 debug 测试时。我们发现 in 和 out 指令都不支持单步中断。**

## 检测点 14.2

编程，用加法和移位指令计算 \(ax\)=\(ax\)_1_0。

提示，（ax\)10=\(ax\)2+\(ax\)8 。

```text
assume cs:code
code segment
start:
    mov bx, ax
    shl ax,1    ; (ax)=(ax)*2
    mov cl,3    ; 移动次数大于 1 必须放到 cl
    shl bx,cl   ; (bx)=(ax)*8
    add ax,bx   ; (ax)=(ax)*8+(ax)*2
    mov 4c00h
    int 21h
code ends
end start
```

### 参考链接

* 汇编语言（王爽第三版）检测点14 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6883985.html](https://www.cnblogs.com/Base-Of-Practice/articles/6883985.html)

