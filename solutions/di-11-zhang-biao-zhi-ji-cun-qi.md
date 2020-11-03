# 第 11 章：标志寄存器

## 检测点 11.1

写出下面每条指令执行后，ZF、PF、SF 等标志位的值。

| 指令 | ax | bx | ZF | PF | SF |
| :--- | :--- | :--- | :---: | :---: | :---: |
| sub al,al | 0000 |  | 1 | 1 | 0 |
| mov al,1 | 0001 |  | - | - | - |
| push ax | \(0001\) |  | - | - | - |
| pop bx |  | 0001 |  | - | - |
| add al,bl | 0002 |  | 0 | 0 | - |
| add al,10 | 0012 |  | - | 1 | - |
| mul al | 0144 |  | - | - | - |

## 检测点 11.2

| 指令 | ax | CF | OF | SF | ZF | PF |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| sub al,al | 0000 | 0 | 0 | 0 | 1 | 1 |
| mov al,10H | 0010 | - | - | - | - | - |
| add al,90H | 00a0 | - | - | 1 | 0 | - |
| mov al,80H | 0080 | - | - | - | - | - |
| add al,80H | 0000 | 1 | 1 | 0 | 1 | - |
| mov al,0FCH | 00fc | - | - | - | - | - |
| add al,05H | 0001 | 1 | 0 | - | 0 | - |
| mov al,7DH | 007d | - | - | - | - | - |
| add al,0BH | 000b | 0 | 1 | 1 | - | 1 |

### 参考链接

* 汇编语言（王爽第三版）检测点11 - 筑基2017 - 博客园 
  * [https://www.cnblogs.com/Base-Of-Practice/articles/6883947.html](https://www.cnblogs.com/Base-Of-Practice/articles/6883947.html)

