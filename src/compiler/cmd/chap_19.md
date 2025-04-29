以下這段程式碼
``` cpp
#include <cstdio>

struct T {
  int arr[1];
  ~T() { std::printf ("~T()\n"); }
  const int *begin () const { return &arr[0]; }
  const int *end () const { return &arr[1]; }
};

const T& f(const T& t) { return t; }
T g() { return T{42}; }

int main ()
{
  for (auto e : f (g ()))
    std::printf ("loop\n");
}
```
在C++20的結果是
``` bash
~T()
loop
```
不過跟我們所期望的
``` bash
loop
~T()
```
不太相同，因此C++23之後，延長了for loop的temporaries variable的lifecycle
GCC15之後，可以使用
- `-frange-for-ext-temps`
- `fno-range-for-ext-temps`
來明確控制這邊的行為
