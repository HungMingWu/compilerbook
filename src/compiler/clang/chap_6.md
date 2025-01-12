在clang的builtin function中，這個是最實用的，應該特別提出來談一下
雖說C++26可能有反射，不過距離實用還很久，況且C語言也用不上，這個就是窮人版的反射，使用起來也很簡單
``` c
struct S {
  int x, y;
  float f;
  struct T {
    int i;
  } t;
};

void func(struct S *s) {
  __builtin_dump_struct(s, printf);
}
```
只要呼叫`__builtin_dump_struct`，就能印出struct中的內容，可能的結果如下
``` bash
struct S {
  int x = 100
  int y = 42
  float f = 3.141593
  struct T t = {
    int i = 1997
  }
}
```
不過目前也只有clang能用
