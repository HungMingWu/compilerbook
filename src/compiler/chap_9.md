在看SIMD的資料時，發現了gcc/clang有實作vector extension，故記錄一下
如果不用vector extension的話，要處理SIMD除了inline assemlby，就只有Intrinsics function能考慮了
以下是一個Intrinsics SIMD 加法
``` c
#include <immintrin.h>
__m128i add2_intrinsics(__m128i a, __m128i b)
{
    return _mm_add_epi32(a, b);
}
```
不僅難看，並且不同平台的SIMD Intrinsics 還不疼，要同時維護多個平台

如果使用vector extension的話
``` c
typedef int v4si __attribute__ ((vector_size (16)));
v4si add2(v4si a, v4si b)
{
    return a + b;
}
```
優點有
- 容易理解
- 移植到其他平台不那麼困難 (編譯器幫你處理)
缺點也是有
- 不能支援某平台特殊的Intrinsics function
可以直接產生
``` assembly
add2:
        push    rbp
        mov     rbp, rsp
        movdqa  xmmword ptr [rbp - 16], xmm0
        movdqa  xmmword ptr [rbp - 32], xmm1
        movdqa  xmm0, xmmword ptr [rbp - 16]
        paddd   xmm0, xmmword ptr [rbp - 32]
        pop     rbp
        ret
```
這樣的assembly code

#### About clang
clang除了支援gcc的語法之外，還有另外一種extension
``` c
typedef int v4si __attribute__((ext_vector_type(4)));
v4si add2(v4si a, v4si b)
{
    return a + b;
}
```
差異是
- `ext_vector_type`是定義元素個數
- `vector_size`是定義總位元數大小
