程式碼看起來跟OpenMP差不多，不過是基於CUDA Model，為GPU專門設計Model
``` c
#include <stdio.h>

#define N 1000

int main() {
    int a[N], b[N], c[N];

    for (int i = 0; i < N; i++) {
        a[i] = i;
        b[i] = i * 2;
    }

    #pragma acc parallel loop
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
$ gcc -fopenacc vec_add.c -o vec_add
```

#### Offload
同樣的，OpenACC也支持Offload模式，用法也跟OpenMP差不多
``` bash
$ gcc -fopenacc -foffload=nvptx-none  vec_add.c
```
