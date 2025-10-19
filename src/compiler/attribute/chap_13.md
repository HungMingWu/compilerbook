這兩個attribute看起來很像，只存在於gcc/clang的世界，MSVC不支持
語意很像，卻又有些許的不同
先看看以下的程式碼
``` c
int square(int num) {
    return num * num;
}

int sum(int v)
{
    return square(v) + square(v);
}
```
我們不關心`square`，只看`sum`的Assembly Code長這個樣子
``` assembly
sum(int):
        push    rbp
        mov     rbp, rsp
        push    rbx
        sub     rsp, 8
        mov     DWORD PTR [rbp-12], edi
        mov     eax, DWORD PTR [rbp-12]
        mov     edi, eax
        call    square(int)
        mov     ebx, eax
        mov     eax, DWORD PTR [rbp-12]
        mov     edi, eax
        call    square(int)
        add     eax, ebx
        mov     rbx, QWORD PTR [rbp-8]
        leave
        ret
```
這邊`square`被呼叫兩次，但是我們知道square函式沒有任何side effect，可否指呼叫一次就好，這就是`pure`和`const`發揮用處的地方
讓我們修改成
``` c
[[gnu::const]] int square(int num) {
    return num * num;
}
```
`sum`的Assembly Code跟著改變
``` assembly
sum(int):
        push    rbp
        mov     rbp, rsp
        sub     rsp, 8
        mov     DWORD PTR [rbp-4], edi
        mov     eax, DWORD PTR [rbp-4]
        mov     edi, eax
        call    square(int)
        add     eax, eax
        leave
        ret
```
所以可以知道，這個attribute可以告知編譯器，這邊有最佳化的可能
植於`pure`和`const`的差異，在於`pure`可以讀取全域變數的值，而`const`不行
在`const`中讀取全域變數是UB，目前static analyzer無法有效偵測
