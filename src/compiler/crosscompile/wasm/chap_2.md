從上一節的範例開始修改
``` c
int add(int a, int b) {
        return a + b;
}
```
由於libc比較複雜，先從這個範例開始
``` bash
$ clang --target=wasm32 -nostdlib -Wl,--no-entry -Wl,--export-all  -o add.wasm add.c
```
其中
- `-Wl,--no-entry`告訴linker，我們沒有entry point，跟上面emcc的情況類似
- `-Wl,--export-all`一樣告訴linker，我們要輸出所有的symbol
- `-nostdlib`告訴我們不使用C standard library
#### wasi-sdk
在編譯C/C++時，libc/libc++ for Webassembly是必要的
.首先到[wasi-sdk](https://github.com/WebAssembly/wasi-sdk)下載最新的SDK
``` bash
$ wget https://github.com/WebAssembly/wasi-sdk/releases/download/wasi-sdk-24/wasi-sdk-24.0-x86_64-linux.tar.gz
$ tar zxvf wasi-sdk-24.0-x86_64-linux.tar.gz
```
#### About C
修改上面的那個add
``` c
#include <stdio.h>
int add(int a, int b) {
        printf("add invoked\n");
        return a + b;
}
```
然後可以用clang編譯了
``` bash
$ clang -target wasm32-wasi --sysroot=~/wasi-sdk-24.0-x86_64-linux/share/wasi-sysroot -o add.wasm add.c -Wl,--export-all
```
跟上面不同的地方
- 現在target是wasm-wasi，而非wasm32了
- 指定sysroot的目錄，指向我們下載的wasi-sdk

#### About C++
這邊是一個使用到STL的範例

``` cpp
#include <algorithm>
extern "C"
int max(int a, int b) {
        return std::max(a, b);
}
```

編譯起來比上面複雜一點
``` bash
$ clang -target wasm32-wasi --sysroot=~/wasi-sdk-24.0-x86_64-linux/share/wasi-sysroot -lc++ -lc++abi -o max.wasm max.cpp -Wl,--export-all
```
經過測試，`-lc++ -lc++abi`到目前為止是必要的
