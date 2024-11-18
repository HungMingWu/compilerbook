這就是所謂的Combo技：必須先看過這兩個部分
- `-Wunsafe-buffer-usage`
- `libc++ hardening`
先看一下出問題的Code
``` cpp
#include <stddef.h>

int get_last_element(int *pointer, size_t size) {
        return pointer[size - 1];
}

int main()
{
        int arr[] = {1, 2, 3};
        return get_last_element(arr, 0);
}
```
直接編譯的話，看不出什麼問題，不過arrray 的index出錯了，編譯器卻沒有任何表示
``` bash
$ clang test.cpp -o test
$ ./test
```
不過新版的clang有了`-Wunsafe-buffer-usage`之後，終於有提示了
``` cpp
$ clang test.cpp -o test -Wunsafe-buffer-usage
test.cpp:5:10: warning: unsafe buffer access [-Wunsafe-buffer-usage]
    5 |   return pointer[size - 1];
      |          ^~~~~~~
1 warning generated.
```
至於要怎麼修正
- 非C++部分的使用`-fbounds-safety`(還沒做))
- C++的部份透過`libc++ hardening`
### Example for std::span
讓我們將程式改成這樣
``` cpp
#include <stddef.h>
#include <span>

int get_last_element(std::span<int> views) {
        return views[views.size() - 1];
}

int main()
{
        int arr[] = {1, 2, 3};
        return get_last_element(std::span<int>(arr, 0));
}
```
``` bash
$ clang test.cpp -o test -std=c++20 -stdlib=libc++ -lc++ -Wunsafe-buffer-usage -D_LIBCPP_HARDENING_MODE=_LIBCPP_HARDENING_MODE_DEBUG
$ ./test
/usr/local/bin/../include/c++/v1/span:517: assertion __idx < size() failed: span<T>::operator[](index): index out of range
Aborted (core dumped)
```
直接Abort，至少比起Undefined behavior要來的安全，也比較容易找到修正的點
修正的方法也很簡單
``` cpp
int main()
{
        int arr[] = {1, 2, 3};
        return get_last_element(arr);
}
```

