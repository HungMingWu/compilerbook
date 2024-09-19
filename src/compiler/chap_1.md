所謂的`Intrinsic function`，作用跟一般的function沒有什麼兩樣，唯一的差異是在於它是由Compilier所提供

它有以下特點
- 由於是Compilier提供的，所以可以根據上下文對其最佳化，或者做出更精確地分析
	- 例如透過不同的指令集作加速
- 很多`Intrinsic function`是Compiler所獨有，MSVC有的，gcc/clang可能不會有

#### SIMD example

SIMD就是最著名的`Intrinsic function`，透過SSE/AVX指令集，來為原先的程式做加速
例如上面等價的程式碼
``` cpp
#include <immintrin.h>
__m256 multiply_and_add(__m256 a, __m256 b, __m256 c) {
  return _mm256_fmadd_ps(a, b, c);
}
```
#### builtin function
`builtin function`很類似`Intrinsic function`，不過有些不同
- `builtin function`是gcc/clang獨有的，MSVC沒有
- `builtin function`的prefix都是以`__builtin_`開始的
- `Intrinsic functions`比較偏重於硬體相關，而`builtin function`比較偏重於功能和移植性

#### __has_builtin

gcc/clang都有支援，在Compile time時偵測是否某個builtin function存在
這裡有一個範例
``` c
int popcount(int x)
{
#if __has_builtin(__builtin_popcount)
        return __builtin_popcount(x);
#else
        int count = 0;
        for (; x != 0; x &= x - 1)
                count++;
        return count;
#endif
}
```


