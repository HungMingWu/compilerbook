有些時候，你想關閉程式行間的某些Warning，又不想全部關閉
例如以下的程式碼有問題，但是我們還是想跳過檢查，該怎麼做
``` c
printf("%s", 123);
```
#### gcc/clang's way, pragma diagnostic
使用gcc/clang的pragma命令，將diagnostic中的某個warning ignored
``` c
#include <stdio.h>
int main()
{
#pragma GCC diagnostic push
#pragma GCC diagnostic ignored "-Wformat"
        printf("%s\n", 123);
#pragma GCC diagnostic pop
}
```
對於clang來說，上面的GCC可以代換成clang，不過置換過的程式碼對gcc無效
#### MSVC's way
MSVC雖然也是用pragam，不過使用方式大相逕庭，上面的範例用MSVC改寫是這個樣子
``` c
#include <stdio.h>
int main()
{
#pragma warning(push)
#pragma warning(disable : 4313)
#pragma warning(disable : 4477)
        printf("%s\n", 123);
#pragma warning(pop)
}
```


