之前介紹過，CMake可以直接產生[JSON compilation database](http://clang.llvm.org/docs/JSONCompilationDatabase.html)，那不支持CMake的專案該怎麼辦
既然是clang相關功能，自然clang有相關的CLI對應
``` bash
$ clang -MJ test1.json -c test1.cpp
$ clang -MJ test2.json -c test2.cpp
```
然後把幾個小的檔案拼湊成`compile_commands.json`就好了，不過這樣好麻煩，想要非侵入式的作法
[Bear](https://github.com/rizsotto/Bear)就是因此而存在的
拿[libuv](https://github.com/libuv/libuv)來舉例
``` bash
$ git clone https://github.com/libuv/libuv
$ cd libuv
$ ./autogen.sh
$ ./configure
$ bear -- make
```
類似的工具還有[compiledb](https://github.com/nickdiego/compiledb)，用法也差不多
