雖然大家都說，常用的Library最好不要自己寫，不過人生總是有那種例外
如果工作上或是興趣需要寫一個printf like的函式，而希望編譯器能夠型別檢查的能力
這就是`__attribute__ ((format (printf, x, y)))` 發揮的地方
``` c
oid debug(const char *fmt, ...) __attribute__((format(printf, 1, 2)));
void debug(const char* fmt, ...)
{
}
int main() {
        debug("hello %p\n", 123);
        return 0;
}
```
編譯就會吐出Warning了
``` bash
$ gcc test.c -o test -Wformat
test.c: In function ‘main’:
test.c:6:23: warning: format ‘%p’ expects argument of type ‘void *’, but argument 2 has type ‘int’ [-Wformat=]
    6 |         debug("hello %p\n", 123);
      |                      ~^     ~~~
      |                       |     |
      |                       |     int
      |                       void *
      |                      %d
```
clang也輸出差不多

#### MSVC
MSVC採用不同於attribute的機制，而是透過[# SAL Annotations](https://learn.microsoft.com/en-us/cpp/c-runtime-library/sal-annotations)的方法，使用方式也跟上面不同
``` cpp
#undef FORMAT_STRING
#if _MSC_VER >= 1400
# include <sal.h>
# if _MSC_VER > 1400
#  define FORMAT_STRING(p) _Printf_format_string_ p
# else
#  define FORMAT_STRING(p) __format_string p
# endif /* FORMAT_STRING */
#else
# define FORMAT_STRING(p) p
#endif /* _MSC_VER */

/* use /analyze or _USE_ATTRIBUTES_FOR_SAL for checking */
void debug(FORMAT_STRING(const char* format), ...)
{

}

int main() {
        debug("hello %p\n", 123);
        return 0;
}

```
同樣編譯
``` powershell
$ cl /analyze test.cpp
Microsoft (R) C/C++ Optimizing Compiler Version 19.44.35217 for x64
Copyright (C) Microsoft Corporation.  All rights reserved.

test.cpp
test.cpp(20) : warning C6066: Non-pointer passed as _Param_(2) when pointer is required in call to 'debug' Actual type: 'int'.
Microsoft (R) Incremental Linker Version 14.44.35217.0
Copyright (C) Microsoft Corporation.  All rights reserved.

/out:test.exe
test.obj
```
gcc/clang吐出來的warning比較容易理解
