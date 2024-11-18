這個不是錯誤，而是提醒你如何增加對於Buffer使用的安全性
看看這段程式碼
``` c
#include <stddef.h>

int get_last_element(int *pointer, size_t size) {
        return pointer[size - 1];
}
```
當`size`為0的時候，會進入undefined behavior，什麼事情都會發生
不過程式本身是沒有問題的
如果我們加上`-Wunsafe-buffer-usage`
``` bash
$ clang -c test.cpp -Wunsafe-buffer-usage
test.cpp:5:10: warning: unsafe buffer access [-Wunsafe-buffer-usage]
    5 |   return pointer[size - 1];
      |          ^~~~~~~
1 warning generated.
```
會告訴我們遇到unsafe buffer access
加強的方式，描述在clang safe buffer的部分，這裡先掠過
#### Suppress unwanted warnings
有些時候，我們不希望warning滿天飛，所以可以這樣做
``` cpp
int get_last_element(int *pointer, size_t size) {
        #pragma clang unsafe_buffer_usage begin
        return pointer[size - 1];
        #pragma clang unsafe_buffer_usage end
}
```
Warning就消失了
#### Unsafe function usage
假設我們現在有個overload function `get_last_element`，而他是safe function
那麼我們將原先的function加上`[[clang::unsafe_buffer_usage]]`屬性
``` cpp
#include <stddef.h>
#include <span>

int get_last_element(std::span<int> sp) {
        return sp[sp.size() - 1];
}

[[clang::unsafe_buffer_usage]]
int get_last_element(int *pointer, size_t size) {
#pragma clang unsafe_buffer_usage begin
        return get_last_element(std::span(pointer, size));
#pragma clang unsafe_buffer_usage end
}

int main()
{
        int arr[3] = {1, 2, 3};
        return get_last_element(arr, 3);
}
```
因此所有用到unsafe function的地方都會被標記出來，照著提示來修改
``` bash
$ clang -c test.cpp -Wunsafe-buffer-usage -std=c++20
test.cpp:21:9: warning: function introduces unsafe buffer manipulation [-Wunsafe-buffer-usage]
   21 |         return get_last_element(arr, 3);
      |                ^~~~~~~~~~~~~~~~~~~~~~~~
test.cpp:21:9: note: pass -fsafe-buffer-usage-suggestions to receive code hardening suggestions
1 warning generated.
```
至於Safe Buffer該怎麼做，留待clang章節來說

