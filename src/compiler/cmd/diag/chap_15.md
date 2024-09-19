這個warning比較特別，只針對C語言起作用
同樣的一段程式碼，在C和C++有不同涵義
``` c
int func();
```
在
- C語言中，func可以接受多個參數
- C++語言中，func不接受任何參數
所以這樣的程式碼在C語言是合法的
``` c
int func();
int main(int argc, char** argv)
{
        func(1, 2, 3);
}
```
同樣的程式碼用C++模式編譯會出現
``` bash
$ clang test.cpp -c
test.cpp:4:2: error: no matching function for call to 'func'
    4 |         func(1, 2, 3);
      |         ^~~~
test.cpp:1:5: note: candidate function not viable: requires 0 arguments, but 3 were provided
    1 | int func();
      |     ^
1 error generated.
```
而
``` bash
$ gcc test.c -c
```
什麼都沒發生，這個選項就是希望我們能夠明確地把function prototype寫清楚
``` bash
$ gcc test.c -c -Wstrict-prototypes
test.c:1:1: warning: function declaration isn’t a prototype [-Wstrict-prototypes]
    1 | int func();
      | ^~~
```
不過C23之後，這種寫法也不支援了
``` bash
$ clang test.c -c
test.c:4:6: warning: passing arguments to 'func' without a prototype is deprecated in all versions of C and is not supported in C23 [-Wdeprecated-non-prototype]
    4 |         func(1, 2, 3);
      |             ^
1 warning generated.
```

