同樣的，這東西也沒在C/C++ Standard中，不過總是有遇到鬼的一天
假設你要開發一個Cryptography library，要用到類似CPU AES Instruction的時候，只有兩種選擇
- 利用Nasm/Yasm直接寫Assembly
- 在C/C++中直接插入Assembly Code
Inline Assembly最大的問題就是不能跨平台，於是乎使用Compiler提供的`intrinsic-function`是大勢所趨，不過凡事都有萬一，以下就是介紹那個萬一

#### gcc/clang
``` c
#include <stdio.h>

int main()
{
    int sum, num1 = 1, num2 = 2;
    asm (
        "addl    %%edx, %%eax\n"
        :"=a"(sum)
        :"a"(num1), "d"(num2)
    );
    printf("sum = %d\n", sum);
    return 0;
}
```
程式邏輯沒什麼可提的
要注意的只有兩點
- Inline Assembly是用`asm(...)` 為表示的
- gcc/clang用的是AT&T語法，而這個語法已經全面輸給Intel Syntax了
	- NASM/YASM矮用的都是Intel Syntax

#### MSVC
對於64bit，MSVC**完全不支援** `Inline Assembly`
只在32bit的環境下可以使用
看到的範例可能是這樣子
``` c
__asm {
	mov al, 2
	mov dx, 0xD007
	out dx, al
}
```
從上面可以觀察到
- MSVC使用的是`__asm {... }`表示式
- MSVC採用的是Intel Syntax
