猜猜看以下的程式碼結果是什麼
``` c
#include <stdio.h>
struct pair {
        char x;
        int y;
};
int main()
{
        printf("%lu\n", sizeof(struct pair));
}
```
不管你是在哪個環境執行(32biit/64bit OS)，結果都不是你預期的5，那是因為為了讓提升CPU使用效率，盡量使用Aligned Memory Access，但是我們也可以告訴Compilier我們不想這麼做
不這麼做的好處也是有
- 由於取消packed，所以不同Compilier所產生的struct layout趨近於相同
- Memory Layout 適合在Network或是File Storage使用
	- 所以可以將電腦上的資料變成一個Memory Stream直接傳輸到手機
	- 而不用複雜的Serialization和Deserialization方式
使用方式
``` c
#include <stdio.h>
struct [[gnu::packed]] pair {
        char x;
        int y;
};
int main()
{
        printf("%lu\n", sizeof(struct pair));
}
```
#### About MSVC
MSVC不支持packed方式，他使用的是pragma push和pop
以上的範例改寫成
``` c
#include <stdio.h>
#pragma pack(push,1)
struct pair {
        char x;
        int y;
};
#pragma pack(pop)
int main()
{
        printf("%lu\n", sizeof(struct pair));
}
```

