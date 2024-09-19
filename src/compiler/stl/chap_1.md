對於`libstdc++`
- 加上`-D_GLIBCXX_ASSERTIONS`，可以對c++中的string, vector, array加上boundary check，不過是以減少Performance為代價，所以建議只在Debug build使用
- 加上`_GLIBCXX_CONCEPT_CHECKS`，可以增加Compile time check的能力，不過只支持C++03之前的版本，詳細請參考[Chapter 16. Compile Time Checks](https://gcc.gnu.org/onlinedocs/libstdc++/manual/ext_compile_checks.html)
- `_GLIBCXX_SANITIZE_VECTOR`：這個比較複雜，需要搭配AddressSanitizer使用
``` cpp
#include <vector>
#include <iostream>

int main ()
{
        std::vector<int> toto;
        toto.push_back(2);
        int const& titi = toto[0];
        toto.pop_back();
        std::cout << titi << std::endl;
        return 1;
}
```
這裡的`titi`指向了一個invalid object，不過這程式單用AddressSanitizer是偵測不出來的
``` bash
$ g++ test.cpp -o test -fsanitize=address
$ ./test
2
```
需要有libstdc++的搭配
``` bash
$ g++ test.cpp -o test -fsanitize=address -D_GLIBCXX_SANITIZE_VECTOR
$ ./test
=================================================================
==2712==ERROR: AddressSanitizer: container-overflow on address 0x502000000010 at pc 0x5d54461f3557 bp 0x7ffe004b44d0 sp 0x7ffe004b44c0
READ of size 4 at 0x502000000010 thread T0
    #0 0x5d54461f3556 in main (/home/hm/test+0x2556) (BuildId: 6b2ab5d3827cb1b3224a1e4600af31478f8b85d6)
    #1 0x726aff22a1c9 in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #2 0x726aff22a28a in __libc_start_main_impl ../csu/libc-start.c:360
    #3 0x5d54461f3324 in _start (/home/hm/test+0x2324) (BuildId: 6b2ab5d3827cb1b3224a1e4600af31478f8b85d6)
```
- `_GLIBCXX_DEBUG` 和 `_GLIBCXX_DEBUG_PEDANTIC`
	- `_GLIBCXX_DEBUG`將編譯成Debug Mode
	- 一旦進入Debug Mode的時候，使用`_GLIBCXX_DEBUG_PEDANTIC`的話，會將libstdc++非標準化的行為視為錯誤
一個簡單的範例
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
在未加入`_GLIBCXX_DEBUG`之前，得不到什麼有用的資訊

``` bash
$ g++ test.cpp -o test
$ ./test
Segmentation fault (core dumped)
```
不過加上之後就不一樣了
``` bash
 $ g++ test.cpp -o test -D_GLIBCXX_DEBUG
 $ ./test
 /usr/include/c++/13/debug/safe_iterator.h:298:
In function:
    gnu_debug::_Safe_iterator<_Iterator, _Sequence, _Category>::reference
    gnu_debug::_Safe_iterator<_Iterator, _Sequence, _Category>::operator*()
    const [with _Iterator = gnu_cxx::normal_iterator<int*, std::vector<int,
    std::allocator<int> > >; _Sequence = std::debug::vector<int>; _Category
    = std::forward_iterator_tag; reference = int&]

Error: attempt to dereference a singular iterator.

Objects involved in the operation:
    iterator "this" @ 0x7fff5544bd10 {
      type = gnu_cxx::normal_iterator<int*, std::vector<int, std::allocator<int> > > (mutable iterator);
      state = singular;
      references sequence with type 'std::debug::vector<int, std::allocator<int> >' @ 0x7fff5544bd40
    }
Aborted (core dumped)
```
`_GLIBCXX_DEBUG`不像`_GLIBCXX_ASSERTIONS`，是ABI Breaking的，所以如果要加入這定義，整個Solution需要重新編譯
