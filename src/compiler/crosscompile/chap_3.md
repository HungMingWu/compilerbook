這邊沒有要介紹zig這門程式語言，而是指介紹Cross compile C/C++的部分，這是上一章clang的強化版，不必準備sysroot了，因為zig都幫你準備好了
``` bash
$ zig cc -target arm-linux-gnueabihf hello.c
```
#### Makefile
也不用像之前那麼麻煩，只要在Makefile中將`CC=gcc`的部分改成`CC=zig cc -target ...`就好了，可以解決95%的問題

#### CMake
也不用特別設定，只要
``` bash
CXX="zig cc -target ..." cmake -S -B build
```
也能解決95%的問題
#### tradional configure
``` bash
$ CXX="zig cc -target ...." ./configure
$ make
```
難怪這方式越來越流行
