inline這個關鍵字已經是有名無實，不管你有沒有加inline，編譯器都會自動評估是否inline

但如果我們強制要inline或不inline時該怎麼做
#### Always inline
gcc/clang的寫法
``` c
[[gnu::always_inline]] inline void foo() {}
```
這裡的inline如果不寫，gcc會跳出warning，而clang不會
而MSVC的寫法
``` c
__forceinline void foo() {}
```
MSVC加了inline也是會跳出warning
#### Never inline
剛好和上面的用途相反
gcc和clang的寫法
``` cpp
[[gnu::noinline]] void foo() {}
```
MSVC則是
``` cpp
__declspec(noinline) void foo() {}
```
MSVC的always_inline和noinline的寫法完全不同，真有你的
#### Macro
為了跨編譯器使用，用個Macro包裝起來是最保險的作法
``` c
#if defined(_MSC_VER)
#  define ALWAYS_INLINE __forceinline
#  define NEVER_INLINE __declspec(noinline)
#elif defined(__GNUC__) || defined(__clang__)
#  define ALWAYS_INLINE inline __attribute__((always_inline))
#  define NEVER_INLINE __attribute__((noinline))
#else
#  define ALWAYS_INLINE inline
#  define NEVER_INLINE
#endif
```

#### artificial (gcc/clang only)
這是一個比較難懂的attribute，對生成machine code沒有影響，只對Debugger有用
``` c
10: static inline int foo(struct q *x)
11: {
12:     return bar(x + 1);
13: }

20: void baz(void)
21: {
22:     x = foo(qa);
23:     x = foo(qb);
24: }
```
假設bar掛了，我們從Debugger看到的資訊大概如下
``` bash
   #0  0x00000000004b1a2a in bar (x=0x8) at foo.c:5 
   #1  0x0000000000416ee0 in baz () at foo.c:12
   #2  0x0000000000413fab in main () at foo.c:30
```
看到一個frame，由於foo被inline了，他指向`bar(x + 1)`這一行，而我們無法判斷是`qa`或是`qb`出錯
加了`artificial`之後，會告訴編譯器：這個函數是「人造的」(或只是個包裝)，在生成除錯資訊時，應盡可能使用**呼叫該函數的位置**的資訊，而不是函數體內部的行號。
所以看到的結果可能會變成這樣
``` bash
   #0  0x00000000004b1a2a in bar (x=0x8) at foo.c:5
   #1  0x0000000000416ee0 in baz () at foo.c:22
   #2  0x0000000000413fab in main () at foo.c:30
```
這邊就指向第22行，告訴我們是qa出問題了
MSVC沒有類似的功能
