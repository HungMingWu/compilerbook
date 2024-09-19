有些時候，我們需要寫自己的Allocator，於是有類malloc的API產生
### malloc like (gcc/clang)

``` cpp
[[malloc]]
void *my_malloc(size_t size);
```
這告訴Compilier，這個函數索回傳是分配過的Memory，能夠幫助Compiler做進一步的Optimization
### Ownership (clang only)
有些時候希望static analyzer能夠幫我們抓出可能的錯誤錯誤
可以加Ownership的attribute，以下是一個簡單的範例
``` cpp
#include <stdlib.h>
[[clang::ownership_returns(my_malloc1)]]
void *my_malloc1(size_t size);

[[clang::ownership_takes(my_malloc1, 1)]]
void my_free1(void *);

[[clang::ownership_returns(my_malloc2)]]
void *my_malloc2(size_t size);

[[clang::ownership_takes(my_malloc2, 1)]]
void my_free2(void *);

void test()
{
        char* p = my_malloc1(1);
        *p = 0;
}
void test1()
{
        char* p = my_malloc2(1);
        my_free1(p);
}


```
上面的例子當中
- test是malloc之後沒有free
- test1中，allocate和free的Allocator不一樣
然後我們使用clang檢查
``` bash
$ clang --analyze  test.c -std=c23
test.c:26:1: warning: Potential leak of memory pointed to by 'p' [unix.Malloc]
   26 | }
      | ^
test.c:30:2: warning: Memory allocated by 'my_malloc2()' should be deallocated by function that takes ownership of 'my_malloc2', not 'my_free1()', which takes ownership of 'my_malloc1' [unix.MismatchedDeallocator]
   30 |         my_free1(p);
      |         ^~~~~~~~~~~
2 warnings generated.
```
看起來能達到預期的效果
而上面attribute的意思
- `[[clang::ownership_returns(my_malloc1)]]` 代表某個資源是透過`my_alloc1`所分配的
- `[[clang::ownership_takes(my_malloc1, 1)]]`  代表資源將由這個函數所釋放，而這邊的1代表函數的第一個參數

而還有一個attribute `ownership_holds`
以下是個範例
``` c
#include <stdlib.h>
[[clang::ownership_returns(my_malloc1)]]
void *my_malloc1(size_t size);

[[clang::ownership_takes(my_malloc1, 1)]]
void my_free1(void *);

[[clang::ownership_returns(my_malloc2)]]
void *my_malloc2(size_t size);

[[clang::ownership_takes(my_malloc2, 1)]]
void my_free2(void *);

[[clang::ownership_holds(my_malloc1, 1), clang::ownership_holds(my_malloc2, 2)]]
void hold_func(void* p1, void* p2)
{
        my_free1(p1);
        my_free2(p2);
}

void test()
{
        char* p1 = my_malloc1(1);
        char* p2 = my_malloc2(1);
        hold_func(p1, p2);
}
```
這裡表示
- hold_func接管了從兩個資源的所有權，不過不代表hold_func要為資源的釋放負責
所以這樣子也是可行的
``` c
#include <stdlib.h>
[[clang::ownership_returns(my_malloc1)]]
void *my_malloc1(size_t size);

[[clang::ownership_takes(my_malloc1, 1)]]
void my_free1(void *);

[[clang::ownership_returns(my_malloc2)]]
void *my_malloc2(size_t size);

[[clang::ownership_takes(my_malloc2, 1)]]
void my_free2(void *);

[[clang::ownership_holds(my_malloc1, 1), clang::ownership_holds(my_malloc2, 2)]]
void hold_func(void* p1, void* p2)
{
        my_free2(p2);
}

void test()
{
        char* p1 = my_malloc1(1);
        char* p2 = my_malloc2(1);
        hold_func(p1, p2);
        my_free1(p1);
}
```
看起來很雞肋，不如前兩者有用
