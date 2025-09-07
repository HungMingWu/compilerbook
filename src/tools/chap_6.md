雖然編譯器已經有內建的static analyzer，不過各家的分析能力有所不同，所以多一項工具使用沒什麼不好

測試範例還是之前那個
``` c
#include <stdio.h>
#include <stdlib.h>

int main()
{
        int *i = (int*)malloc(sizeof(int));
        *i = 1;
        printf("Hello, World! %d\n", *i);
        return 0;
}
```
這邊介紹的是常用的兩種
#### Cppcheck
簡單r，輕量
``` bash
$ cppcheck test.c
Checking test.c ...
test.c:9:9: error: Memory leak: i [memleak]
        return 0;
        ^
```
更多的使用方式可以上網查詢文件
#### clang-tidy
顧名思義，這是clang家族中的一員，不過可以獨立出clang使用，功能遠較Cppcheck多，不過這邊也只介紹最簡單的用法
``` bash
$ clang-tidy test.c
Error while trying to load a compilation database:
Could not auto-detect compilation database for file "test.c"
No compilation database found in /home/hm/ebook or any parent directory
fixed-compilation-database: Error while opening fixed database: No such file or directory
json-compilation-database: Error while opening JSON database: No such file or directory
Running without flags.
1 warning generated.
test.c:8:9: warning: Potential leak of memory pointed to by 'i' [clang-analyzer-unix.Malloc]
    8 |         printf("Hello, World! %d\n", *i);
      |         ^
test.c:6:24: note: Memory is allocated
    6 |         int *i = (int*)malloc(sizeof(int));
      |                        ^~~~~~~~~~~~~~~~~~~
test.c:8:9: note: Potential leak of memory pointed to by 'i'
    8 |         printf("Hello, World! %d\n", *i);
      |     
```
#### Integrate to CMake
如果Hello World這種等級的程式，無法發揮出static analyzer真正的價值，在CMake這種事實上標準的Build System下，整合進去才會發揮最大的功效，這邊的內容會在CMake那張傑討論

