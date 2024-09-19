RTTI是什麼就不說了，有些時候(例如嵌入式開發)，除了Exception之外，你也希望禁用RTTI
``` cpp
#include <typeinfo>
int main()
{
        const auto &info = typeid(int);
        return 0;
}
```
用`-fno-rtti`編譯它
``` bash
$  g++ test.cpp -o test -fno-rtti
test.cpp: In function ‘int main()’:
test.cpp:4:38: error: cannot use ‘typeid’ with ‘-fno-rtti’
    4 |         const auto &info = typeid(int);
      |
```
clang也同樣支援這個command
#### About MSVC
跟Exception的情況類似，文件中提到`/GR`和`/GR-`來開啟和關閉RTTI的部分，不過就算關閉了，上面的範例還是編譯的過，且文件中提到的是
>  **/GR-** 可能會產生較小的映射

不過就這個Case來說，檔案也沒有變小，可能需要更複雜的情況才能試出差異
