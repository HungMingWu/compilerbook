看到Rust支持naked之後，發現我還沒介紹過這個attribute

這個attribute只有gcc和MSVC支持，**clang**是不支持的，用途是將參數放入stack和迴傳時將參數從stack彈出來的部分省略，詳細的部分看Source Code比較快
``` c
int add1(int v)
{
    return v + 1;
}
```
產生的Assembly Code長這樣
``` assembly
"add1(int)":
        push    rbp
        mov     rbp, rsp
        mov     DWORD PTR [rbp-4], edi
        mov     eax, DWORD PTR [rbp-4]
        add     eax, 1
        pop     rbp
        ret
```
如果我們改成這樣
``` c
__attribute__((naked)) int add1(int v)
{
    return v + 1;
}
```
產生的Assembly Code就不同了
```  assembly
"add1(int)":
        mov     eax, edi
        add     eax, 1
        ud2
```
由於這不是一般化的解法，用在對速度特別要求的關鍵路徑上，**除非你真的知道你在幹什麼**，否則不要使用

MSVC使用的方式和gcc不同
``` c
__declspec(naked) int add1(int v);
```
不過MSVC限制一大堆，除非有必要，一樣不建議使用
