Emscripten是基於clang開發的，不過跟原先的clang又不太相同，加入了很多自己的元素進去，例如和Browser互動的功能，OpenGL等，不過有時候你不需要這些功能，而透過Emscripten產生的wasm有點大，所以有其他方法產生WASM檔

#### Installation
``` bash
$ git clone https://github.com/juj/emsdk.git
$ cd emsdk
$ ./emsdk install --system latest
$ ./emsdk activate latest
```
#### Setup Environment
``` bash
$ source emsdk_env.sh
```
這樣就能使用了

#### Example
這範例沒什麼特別的，主要示範一下`Emscripten`如何處理`libc`的部分，同樣的部分在clang可以再做一次
``` c
#include <stdio.h>
#include <emscripten/emscripten.h>

EMSCRIPTEN_KEEPALIVE
int add(int a, int b) {
        printf("add invoked\n");
        return a + b;
}

```
編譯它
``` bash
$ emcc add.c -o add.wasm --no-entry -O3
```
因為我們沒有main存在，所以使用`--no-entry`告知編譯器

#### How to verify
不需要Browser，也不需要寫Javascript，使用Wasmtime就可以了
Wasmtime是個WebAssembly runtime，適合驗證我們的輸出
先安裝Wasmtime
``` bash
$ curl https://wasmtime.dev/install.sh -sSf | bash
```
接著執行剛剛生成的wasm
``` bash
$ wasmtime run --invoke add add.wasm 1 2
warning: using `--invoke` with a function that takes arguments is experimental and may break in the future
add invoked
warning: using `--invoke` with a function that returns values is experimental and may break in the future
3
```
一切符合預期
