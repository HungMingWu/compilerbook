為了不污染正常的編譯環境，決定將musl裝至其他目錄
``` bash
$ git clone git://git.musl-libc.org/musl
$ cd musl
$ ./configure --prefix=$HOME/musl_build
$ make install
```
現在還是以Hello world當範例
``` c
#include <stdio.h>
int main()
{
        printf("Hello world!\n");
        return 0;
}
```
直接用musl提供的`musl-gcc`來編譯
``` c
$ /musl_build/bin/musl-gcc test.c -o test -static
```
如果你仔細看musl-gcc裡面的內容
``` bash
#!/bin/sh
exec "${REALGCC:-gcc}" "$@" -specs "/home/musl_build/lib/musl-gcc.specs"
```
可以直接用gcc和spec來搭配就好
