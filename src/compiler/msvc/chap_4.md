雖然`volatile`這個類型限定詞是C++標準的，不過msvc本身對其有做些非標準的加強
在編譯的時候加入
- `/volatile:ms` 使用microsoft自己的語意
- `/volatile:iso` 使用ISO定義的語意
而在不同的平台下，編譯器的預設選項會不同，可以參考連結
以下為測試程式
``` cpp
#include <iostream>
#include <thread>

volatile int flag = 0;
int data = 0;

void writer() {
    data = 42;
    flag = 1;
}

void reader() {
    while (flag == 0) ;
    std::cout << "data = " << data << std::endl;
}

int main() {
    std::thread t1(writer);
    std::thread t2(reader);

    t1.join();
    t2.join();
}
```
不過非標準的東西還是少用，乖乖用std::atomic吧
#### Reference
，[/volatile (volatile Keyword Interpretation) ](https://learn.microsoft.com/en-us/cpp/build/reference/volatile-volatile-keyword-interpretation)
