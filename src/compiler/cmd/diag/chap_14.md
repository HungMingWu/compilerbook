看看這段程式碼
``` c
#include <stdint.h>

int main(int argc, char** argv)
{
        int x = 0;
        {
                int x = 1;
        }
}
```
``` bash
$ gcc test.c -c -Wshadow
test.c: In function ‘main’:
test.c:7:21: warning: declaration of ‘x’ shadows a previous local [-Wshadow]
    7 |                 int x = 1;
      |                     ^
test.c:5:13: note: shadowed declaration is here
    5 |         int x = 0;
      |       
```
`{}`建立一個新的scope，而裡面的x掩蓋了上一層的x，這個不一定是錯誤，所以要謹慎判斷

