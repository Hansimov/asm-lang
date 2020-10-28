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

### 参考链接：

* 汇编语言（王爽第三版）检测点10 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6883922.html](https://www.cnblogs.com/Base-Of-Practice/articles/6883922.html)

