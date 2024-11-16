libc++也有向libstdc++那樣的加強機制，不過用法不太一樣
用Marco定義`_LIBCPP_HARDENING_MODE`的值
而值可能是以下這幾種
- `_LIBCPP_HARDENING_MODE_FAST`
- `_LIBCPP_HARDENING_MODE_EXTENSIVE`
- `_LIBCPP_HARDENING_MODE_DEBUG
拿這個當範例
``` cpp
#include <vector>
int main() {
        std::vector<int> empty;
        empty[0] = 10;
        return 0;
}
```
然後編譯並執行
``` bash
$ clang test.cpp -o test -stdlib=libc++ -lc++ -D_LIBCPP_HARDENING_MODE=_LIBCPP_HARDENING_MODE_DEBUG
$ ./test
/usr/local/bin/../include/c++/v1/vector:1435: assertion __n < size() failed: vector[] index out of bounds
Aborted (core dumped)
```
不過拿libstdc++的例子來試
``` cpp
#include <vector>
int main() {
        std::vector<int> data{};
        auto it = data.begin();
        data.push_back(42);
        int v = *it;
        return 0;
}
```
libc++測不出問題來，目前clang hardening還在逐漸加強中，所以測試效果沒有這麼好
所以
- 一段程式最好用兩個以上的編譯器編譯過，避免語法問題
- 做CI/CD測試的時候，最好能用多個不同的環境設定來交叉測試
