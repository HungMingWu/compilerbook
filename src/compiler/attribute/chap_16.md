Tail Call Optimization的用處在這裡不細說，這邊只提供編譯器的屬性支援
先看看這段程式
``` c
long long factorial_tail(int n, long long accumulator) {
    if (n == 0) return accumulator;
    
    return factorial_tail(n - 1, n * accumulator);
}
```
為了看出TCO的效果，編譯時代入`-O0`禁止最佳化
三家產生的assemlbly code不相同，這邊拿clang的結果做解說，因為clang是第一個支援TCO的編譯器
```  asm
factorial_tail(int, long long):
        push    rbp
        mov     rbp, rsp
        sub     rsp, 32
        mov     dword ptr [rbp - 12], edi
        mov     qword ptr [rbp - 24], rsi
        cmp     dword ptr [rbp - 12], 0
        jne     .LBB0_2
        mov     rax, qword ptr [rbp - 24]
        mov     qword ptr [rbp - 8], rax
        jmp     .LBB0_3
.LBB0_2:
        mov     edi, dword ptr [rbp - 12]
        sub     edi, 1
        movsxd  rsi, dword ptr [rbp - 12]
        imul    rsi, qword ptr [rbp - 24]
        call    factorial_tail(int, long long)
        mov     qword ptr [rbp - 8], rax
.LBB0_3:
        mov     rax, qword ptr [rbp - 8]
        add     rsp, 32
        pop     rbp
        ret
```
可以看到`call    factorial_tail(int, long long)`這行，會建立新的stack，當深度過大的時候就會造成Stack overflow
接著就是三家編譯器的回合了
#### clang
clang是第一個支持TCO的，讓我們修改原始的程式
``` c
long long factorial_tail(int n, long long accumulator) {
    if (n == 0) return accumulator;
    
    [[clang::musttail]] return factorial_tail(n - 1, n * accumulator);
}
```
產生的Assemlby Code也跟著改變了
``` asm
factorial_tail(int, long long):
        push    rbp
        mov     rbp, rsp
        mov     dword ptr [rbp - 12], edi
        mov     qword ptr [rbp - 24], rsi
        cmp     dword ptr [rbp - 12], 0
        jne     .LBB0_2
        mov     rax, qword ptr [rbp - 24]
        mov     qword ptr [rbp - 8], rax
        jmp     .LBB0_3
.LBB0_2:
        movsxd  rsi, dword ptr [rbp - 12]
        mov     edi, esi
        dec     edi
        mov     rax, qword ptr [rbp - 24]
        imul    rsi, rax
        pop     rbp
        jmp     factorial_tail(int, long long)
.LBB0_3:
        mov     rax, qword ptr [rbp - 8]
        pop     rbp
        ret
```
原先的`call`變成了`jmp`，成功的將recursive變成了loop
#### gcc
gcc15正式支援TCO，所以它不僅支援`[[clang::musttail]]`，同時也支持`[[gnu::musttail]]`，兩者是等價的
#### msvc
MSVC 17.13+之後也支持這功能，不過它使用的attribute是`[[msvc::musttail]]

所以如果要跨平台使用的話，目前Macro還是唯一解

``` c
#if defined(__clang__)
    #define MUSTTAIL [[clang::musttail]]
#elif defined(__GNUC__) && __GNUC__ >= 15
    #define MUSTTAIL [[gnu::musttail]]
#elif defined(_MSC_VER) && _MSC_VER >= 1943
    #define MUSTTAIL [[msvc::musttail]]
#else
    #define MUSTTAIL
#endif
```

