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

## 检测点 10.3

下面的程序执行后，ax 中的数值为多少？

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x5185;&#x5B58;&#x5730;&#x5740;</th>
      <th style="text-align:left">&#x673A;&#x5668;&#x7801;</th>
      <th style="text-align:left">&#x6C47;&#x7F16;&#x6307;&#x4EE4;</th>
      <th style="text-align:left">ax</th>
      <th style="text-align:left">bx</th>
      <th style="text-align:left">CS</th>
      <th style="text-align:left">IP</th>
      <th style="text-align:left">&#x6808;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">1000: 0</td>
      <td style="text-align:left">b8 00 00</td>
      <td style="text-align:left">mov ax,0</td>
      <td style="text-align:left">0000h</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">1000: 3</td>
      <td style="text-align:left">9A 09 00 00 10</td>
      <td style="text-align:left">call far ptr s</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">1000h</td>
      <td style="text-align:left">0008h</td>
      <td style="text-align:left">
        <p>0008h</p>
        <p>1000h</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">1000: 8</td>
      <td style="text-align:left">40</td>
      <td style="text-align:left">inc ax</td>
      <td style="text-align:left">&#xFF08;&#x8BE5;&#x884C;&#x672A;&#x6267;&#x884C;&#xFF09;</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">1000: 9</td>
      <td style="text-align:left">58</td>
      <td style="text-align:left">s: pop ax</td>
      <td style="text-align:left">0008h&#xFF08;IP&#xFF09;</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">1000h</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">add ax , ax</td>
      <td style="text-align:left">0010h</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">pop bx</td>
      <td style="text-align:left"></td>
      <td style="text-align:left">1000h&#xFF08;CS&#xFF09;</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">add ax,bx</td>
      <td style="text-align:left"><b>1010h</b>
      </td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
  </tbody>
</table>

## 检测点 10.4

下面的程序执行后，ax 中的数值为多少？

| 内存地址 | 机器码 | 汇编指令 | ax | IP | 栈 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1000: 0 | b8 06 00 | mov ax,6 | 0006h | 0003h |  |
| 1000: 3 | ff d0 | call ax | （跳转到 0006h） | 0005h | 0005h |
| 1000: 5 | 40 | inc ax |  |  |  |
| 1000: 6 |  | mov bp,sp |  |  |  |
|  |  | add ax, \[bp\] | **000Bh** |  |  |

## 检测点 10.5

（1）下面的程序执行后，ax 中的数值为多少？（注意：用 call 指令的原理来分析，不要在 Debug 中单步跟踪来验证你的结论。对于此程序，在 Debug 中单步跟踪的结果，不能代表 CPU 的实际执行结果。）

```text
assume cs:code

stack segment
    dw 8 dup (0)
stack ends

code segment
start:
    mov ax,stack            ; ax 存储 stack 的段地址
    mov ss,ax               ; ss 指向 stack 段
    mov sp,16               ; sp = 16，而 stack 是 8 字 == 16 字节，正好指向栈顶
    mov ds,ax               ; ds 指向 stack 段
    mov ax,0                ; ax = 0000h
    call word ptr ds:[0EH]  ; 将 CS 和 IP 分别压栈，此时 IP 为该指令后的第一个字节
                            ; 跳转地址为 ds:0E ~ ds:0F，而 ds 指向 stack 段，且栈顶恰好为第 16 个字节
                            ; 因此该 call 指令即跳转到栈顶存储的 CS 和 IP 指向的位置
                            ; 而栈顶存储的 IP 恰好指向的是下一条指令（inc ax）的地址
                            ; 故这条指令执行完，直接执行下面的指令
    inc ax                  ; ax = 0001h
    inc ax                  ; ax = 0002h
    inc ax                  ; ax = 0003h <- ax 最后的值
    mov ax,4c00h
    int 21h
code ends

end start
```

为什么不让使用 debug 来逐步调试？因为在执行到 CALL 指令时，显示 ds:\[0eh\]＝065D（在我的环境中debug 后），ds:\[0eh\] 中的内容是中断的信息，并不是我们程序所希望的 0011H，它一下就 jmp 到 065D 的地址去了，而不是 CS:11 地址执行。

（2）下面的程序执行后， ax 和 bx 中的数值为多少？

```text
assume cs:code

data segment
    dw 8 dup (0)
data ends

code segment
start:
    mov ax,data                     ; ax = data 段地址
    mov ss,ax                       ; ss 指向 data 段
    mov sp,16                       ; sp = 16
    mov word ptr ss:[0] , offset s  ; ss:[0] 指向 s 的偏移地址
    mov ss:[2],cs                   ; ss:[2] 指向段地址 CS
    call dword ptr ss:[0]           ; 分别将 CS 和 IP 压栈，sp = 12 = 0cH
                                    ; CS 位于 ss:[0eH]，IP 位于 ss:[0cH]
                                    ; 入栈的 IP 为该指令后的第一个字节的偏移地址
                                    ；也即下面 nop 对应的地址
                                    ; 执行完跳转至 ss:[0]，也即 s
    nop                             ; 机器码 90，占据一字节
s:
    mov ax,offset s                 ; ax = s 的偏移地址
    sub ax,ss:[0cH]                 ; ax = ax - ss:[0cH]，也即：
                                    ; ax = s 的偏移地址 - nop 的偏移地址，而 nop 为 1字节
                                    ; 故 ax = 0001h
    mov bx,cs                       ; bx = CS 存储的段地址
    sub bx,ss:[0eH]                 ; bx = CS 存储的段地址 - ss:[0eH]
                                    ; 而 ss:[0eH] 中恰好为 CS 地址
                                    ; 故 bx = 0000h
    mov ax,4c00h
    int 21h
code ends

end start
```

### 参考链接

* 汇编语言（王爽第三版）检测点10 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6883922.html](https://www.cnblogs.com/Base-Of-Practice/articles/6883922.html)

