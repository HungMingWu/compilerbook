從[When `static` makes your C code 10 times faster]((https://mazzo.li/posts/c-performance-anecdote.html)找來的範例，可以測試Link time optimization的效果
``` c
#include <inttypes.h>
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

uint64_t modulus = 1ULL << 31; // 2^31

uint64_t loop(uint64_t N, uint64_t S, uint64_t P, uint64_t Q) {
  for (uint64_t i = 0; i < N; i++) {
    S = (S*P+Q) % modulus;
  }
  return S;
}

static uint64_t modulus_static = 1ULL << 31; // 2^31

uint64_t loop_static(uint64_t N, uint64_t S, uint64_t P, uint64_t Q) {
  for (uint64_t i = 0; i < N; i++) {
    S = (S*P+Q) % modulus_static;
  }
  return S;
}

int main(int argc, char *argv[]) {
  if (argc != 5) { return 1; }

  uint64_t N = strtoll(argv[1], NULL, 10);
  uint64_t S = strtoll(argv[2], NULL, 10);
  uint64_t P = strtoll(argv[3], NULL, 10);
  uint64_t Q = strtoll(argv[4], NULL, 10);
  printf("N: %" PRIu64 ", S: %" PRIu64 ", P: %" PRIu64 ", Q: %" PRIu64 "\n", N, S, P, Q);

  struct timespec begin, end;
  clock_gettime(CLOCK_MONOTONIC_RAW, &begin);
  printf("result (non-static): %" PRIu64 "\n", loop(N, S, P, Q));
  clock_gettime(CLOCK_MONOTONIC_RAW, &end);
  printf(" took %d milliseconds\n", (end.tv_nsec - begin.tv_nsec) / 1000000LL + (end.tv_sec  - begin.tv_sec) * 1000LL);

  clock_gettime(CLOCK_MONOTONIC_RAW, &begin);
  printf("result (static): %" PRIu64 "\n", loop_static(N, S, P, Q));
  clock_gettime(CLOCK_MONOTONIC_RAW, &end);
  printf(" took %d milliseconds\n", (end.tv_nsec - begin.tv_nsec) / 1000000LL + (end.tv_sec  - begin.tv_sec) * 1000LL);

  return 0;
}
```
gcc和clang都是加上`-flto`就可以看到效果
``` bash
$ clang -O2 main.c -flto -o main
```

#### Advance
之後，clang提出了ThinLTO，在多數情況下可以進一步做最佳化
使用方式也很簡單
``` bash
$ clang -O2 main.c -flto=thin -o main
```

#### MSVC's version
MSVC也有類似的觀念，不過用起來比較麻煩
``` powershell
$ cl /GL /c test.c
$ link /LTCG test.obj /OUT:test.exe
```
在將Source編譯成object file錢加上`/GL`
而在Linker階段時加上`/LTCG`
不過用上面的例子測試MSVC沒有效果，畢竟各家Compiler實作的方式不相同，所以需要實際跑過，才知道Link Time Optimization是否有用


