這跟C++一點關係都沒有，而是gcc attribute之一
在程式進入點和之後插入執行的函數，例如
``` c
#include <stdio.h>
[[gnu::constructor]]
void before_main() {
        printf("Before main\n");
}

[[gnu::destructor]]
void after_main() {
        printf("After main\n");
}

int main()
{
        printf("main\n");
}
```
編譯並執行
``` bash
$ gcc test.c -o test -std=c2x
$ ./test
Before main
main
After main
```
除此之外，他也能當作shared libary的constructor/destructor
這是test.c
``` c
#include <stdio.h>

[[gnu::constructor]]
void setup(void) {
    printf("setup\n");
}
```
這是consumer main.c
``` c
#include <dlfcn.h>
#include <stdio.h>int main()
{
        void* handle = dlopen("./test.so", RTLD_LAZY);
        if (!handle) {
                printf("Not found shared library\n");
                return -1;
        }
        dlclose(handle);
}
```
編譯並執行
``` bash
$ gcc -o test.so -fPIC test.c -shared
$ gcc main.c -o main
$ ./main
setup
```
#### About MSVC
雖然沒有等價於gcc constructor/destructor的東西，不過不代表不能做到類似的叫果

 如果要在main之前跟之後執行函數，宣告個static variable即可
 ``` cpp
#include <stdio.h>
struct Obj {
        Obj() {
                 printf("Before main\n");
        }
        ~Obj() {
                printf("After main\n");
        }
};
static Obj global_obj;

int main()
{
        printf("main\n");
}
```
如果要在shared libary上實行類似constructor/destructor的語意，則必須在`DllMain`中的ATTACH/DETACH上動手腳
``` c
OOL APIENTRY DllMain( HMODULE hModule,
                       DWORD  ul_reason_for_call,
                       LPVOID lpReserved)
{
    switch (ul_reason_for_call)
    {
    case DLL_PROCESS_ATTACH:
        printf("DllMain, DLL_PROCESS_ATTACH\n");
        break;
    case DLL_THREAD_ATTACH:
        printf("DllMain, DLL_THREAD_ATTACH\n");
        break;
    case DLL_THREAD_DETACH:
        printf("DllMain, DLL_THREAD_DETACH\n");
        break;
    case DLL_PROCESS_DETACH:
        printf("DllMain, DLL_PROCESS_DETACH\n");
        break;
    default:
        printf("DllMain, ????\n");
        break;
    }
    return TRUE;
}
```
