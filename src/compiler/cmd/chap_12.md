有些時候，我們希望我們的execute file不希望dynamic link道libc當中，這樣我們可以將ubuntu編譯過的檔案，放到centos上跑的需求，這時候就需要static link了
範例還是老朋友Hello world
``` c
#include <stdio.h>
int main()
{
        printf("Hello world!\n");
        return 0;
}

```
未static link之前
``` bash
$ gcc test1.c -o test
$ ldd test
        linux-vdso.so.1 (0x00007fff54dcd000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x0000796612e00000)
        /lib64/ld-linux-x86-64.so.2 (0x00007966130e6000)
```
下了`-static` (這邊也可以使用`--static`)
``` bash
$ gcc test.c -o test -static
$ ldd test
        not a dynamic executable
```
clang也同樣支持 `-static`


