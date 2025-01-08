目前三大Compiler都支援OpenMC，不過程度有所不同，至目前為止
- gcc支援到5.x版
- clang完全支援的只有4.5版，更上面的版本都是部分支援
- msvc目前完全支援的只有2.0版，3.0還是實驗性質而已
``` c
#include <stdio.h>
#define N 1000

int main() {
    int a[N], b[N], c[N];
    int i;
    for (i = 0; i < N; i++) {
        a[i] = i;
        b[i] = i * 2;
    }
    #pragma omp parallel for private (i)
    for (i = 0; i < N; i++) {
        c[i] = a[i] + b[i];
    }
    for (i = 0; i < 10; i++) {
        printf("c[%d] = %d\n", i, c[i]);
    }
    return 0;
}
```

``` bash
$ gcc -fopenmp vec_add.c -o vec_add`
```
clang的方式跟gcc一樣

而MSVC的使用方式
``` powershell
$ cl /openmp vec_add.cpp
```
除此之外，clang有對OpenMP對Offload的加強，可以把運算放在GPU上執行，在之後的部分會描述

#### Offload
從4.0之後，OpenMP支持accelerators/coprocessors的運算模式，將一些計算部分移至accelerators/coprocessors上，這種模式稱為offload．類似於Linux Kernrel將Compute交給Network Card計算一樣

至於是4.0之後才有的功能，也就是這裡沒有MSVC的事了

為了支持Offload模式，我們改寫上面的範例
``` c
#include <stdio.h>

#define N 1000

int main() {
    int a[N], b[N], c[N];

    for (int i = 0; i < N; i++) {
        a[i] = i;
        b[i] = i * 2;
    }

    #pragma omp target teams distribute parallel for map(to: a[:N], b[:N]) map(from: c[:N])
    for (int i = 0; i < N; i++) {
        c[i] = a[i] + b[i];
    }

    for (int i = 0; i < 10; i++) {
        printf("c[%d] = %d\n", i, c[i]);
    }

    return 0;
}
```

``` bash
$ gcc -fopenmp -foffload=nvptx-none  vec_add.c -o vec_add
```
在編譯出問題的時候，可能需要手動安裝類似`gcc-offload-nvptx`的Package才能順利進行

至於clang就複雜一點，除了安裝`nvidia-cuda-toolkit`之外，可能還需要`clang-tools`和`libomp-dev`，編譯的方式也複雜一點
``` bash
$ clang -fopenmp -fopenmp-targets=nvptx64 --offload-arch=sm_80  vec_add.c -o vec_add
```

#### Reference
[GCC/OpenMP Update](https://www.openmp.org/wp-content/uploads/2022_Jan_28_ECP_Sollve_OMP_Monthly.pdf)
[OpenMP Offloading in LLVM 15](https://www.openmp.org/wp-content/uploads/OpenMP-Offloading-in-LLVM-15.pdf)
