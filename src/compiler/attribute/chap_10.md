編譯的時候，除了對整個Compilation Unit做Optimization操作之外，有些編譯器可以做更精細的控制(這裡指的就是gcc)
例如以下程式
``` c
int add1(int v)
{
    return v + 1;
}
```
由於夠簡單，gcc直接產出這樣的assembly code
``` assembler
add1(int):
        lea     eax, [rdi+1]
        ret
```
如果我們加上了attribute之後
``` c
int __attribute__((optimize("O0"))) add1(int v)
{
    return v + 1;
}
```
產生的assembly code也跟著變了
``` assembler
add1(int):
        push    rbp
        mov     rbp, rsp
        mov     DWORD PTR [rbp-4], edi
        mov     eax, DWORD PTR [rbp-4]
        add     eax, 1
        pop     rbp
        ret
```
這個功能有時候很有用，特別是在Debug的時候
同樣的，C/C++標準的attribute syntax還無法支持這種語法，必須退回gnu的extension syntax

clang也有類似的機制
``` c
[[clang::optnone]]
int add1(int v)
{
    return v + 1;
}
```
