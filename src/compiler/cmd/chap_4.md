以下功能在gcc/clang可用，MSVC沒有類似的功能
看一下以下程式碼
``` c
int f(int i) {
    return i + 1 > i;
}
```
數學上來說，i+1永遠比i大，不過當i為2147483647時，i+1會鑿成overflow且產生新值-2147483648，因此回傳值應該為0
不過由於signed integer overflow是個undefined behavior，編譯器就可以自行決定怎麼做了，因此它就假設i + 1 永遠比 i 大，然後就直接回傳1了，從
``` assembly
f(int):
        push    rbp
        mov     rbp, rsp
        mov     DWORD PTR [rbp-4], edi
        mov     eax, 1
        pop     rbp
        ret
```
關於`-fwrapv`
gcc的文件上有寫
> This option instructs the compiler to assume that signed arithmetic overflow of addition, subtraction and multiplication wraps around using twos-complement representation.
加上`-fwrapv`之後，所產生的assembly code長這樣
``` assembly
f(int):
        push    rbp
        mov     rbp, rsp
        mov     DWORD PTR [rbp-4], edi
        cmp     DWORD PTR [rbp-4], 2147483647
        setne   al
        movzx   eax, al
        pop     rbp
        ret
```
這樣子才能合乎我們預期的效果
`-ftrapv`類似於`-fwarpv`，不過不同之處在於，在發生overflow之後直接crash掉
由於這個會影響optimization，除非你真的知道在幹什麼，不然99%的情況下都要加．
另外還有一個`-fno-strict-overflow`，類似於`-fwarpv`，不對signed integer overflow做最佳化，唯一不同的是，他不保證運算結果是wrap的，所以這個比較少用

