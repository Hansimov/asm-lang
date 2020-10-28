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

## 检测点 10.2

下面的程序执行后，ax 中的数值为多少？

| 内存地址 | 机器码 | 汇编指令 | ax | IP |
| :--- | :--- | :--- | :--- | :--- |
| 1000: 0 | b8 00 00 | mov ax,0 | 0000h | 0003h |
| 1000: 3 | e8 01 00 | call s |  | 0006h |
| 1000: 6 | 40 | inc ax | （该行未执行） |  |
| 1000: 7 | 58 | s:pop ax | **0006h** | 0008h |

### 参考链接：

* 汇编语言（王爽第三版）检测点10 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6883922.html](https://www.cnblogs.com/Base-Of-Practice/articles/6883922.html)

