眾所周知，gcc所提供的C++  implementation是`libstdc++`，而clang也提供了自己的implementation `libc++`
不過clang自然也可以使用gcc的`libstdc++`
在編譯的時候加上
``` cpp
$ clang test.cpp -o test -stdlib=libstdc++ -lstdc++
```
不過某些Linux distribution中，預設就是使用`libstdc++`了，如果要使用`libc++`
``` cpp
$ clang test.cpp -o test -stdlib=libc++ -lc++
```
這樣就可以了
