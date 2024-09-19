一個char pointer指向const string literal
``` c
#include <stdint.h>

int main(int argc, char** argv)
{
        char* str = "Hello world!\n";
        return 0;
}
```
``` bash
$ gcc test.c -c -Wwrite-strings
test.c: In function ‘main’:
test.c:5:21: warning: initialization discards ‘const’ qualifier from pointer target type [-Wdiscarded-qualifiers]
    5 |         char* str = "Hello world!\n";
      | 
```
順帶一提，同樣的程式碼在C++預設就開啟了`-Wwrite-strings`
``` bash
$ g++ test.cpp -c
test.cpp: In function ‘int main(int, char**)’:
test.cpp:5:21: warning: ISO C++ forbids converting a string constant to ‘char*’ [-Wwrite-strings]
    5 |         char* str = "Hello world!\n";
      |        
```
