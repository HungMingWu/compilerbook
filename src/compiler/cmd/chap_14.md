一般而言，gcc/clang 會到 /usr/lib 和 /usr/include 下找 header files 和 library
當我們要進行Cross compile或是其他需求的時候，可能要在其他地方尋找所需的檔案

例如用clang進行cross compile，由於host是x64，要編譯成aarch64的時候，所需要的header 和 library不同，因此要指定`-sysroot`
``` bash
$ clang --target=arm64 --sysroot=/path/to/aarch64/sysroot -c hello.c
```
