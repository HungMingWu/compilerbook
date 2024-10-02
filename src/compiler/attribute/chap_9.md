目前只有clang支持這兩個attribute，並且不支持‵新式的attribute語法
`diagnose_if`可以在compile-time檢查參數的合法性，並決定是否發出warning或error
``` c
int abs(int a)
        __attribute__((diagnose_if(a >= 0, "Redundant abs call", "warning")));
int must_abs(int a)
        __attribute__((diagnose_if(a >= 0, "Redundant abs call", "error")));
int main()
{
        int val = abs(1); // warning: Redundant abs call
        int val2 = must_abs(1); // error: Redundant abs call
        int val3 = abs(val);
        int val4 = must_abs(val); // Because run-time checks are not emitted for
                                  // diagnose_if attributes, this executes without
                                  // issue.
}
```
編譯結果
``` bash
$ clang -c test.c
test.c:7:17: warning: Redundant abs call [-Wuser-defined-warnings]
    7 |         int val = abs(1); // warning: Redundant abs call
      |                        ^
test.c:2:17: note: from 'diagnose_if' attribute on 'abs':
    2 |         __attribute__((diagnose_if(a >= 0, "Redundant abs call", "warning")));
      |                        ^           ~~~~~~
test.c:8:23: error: Redundant abs call
    8 |         int val2 = must_abs(1); // error: Redundant abs call
      |                              ^
test.c:4:17: note: from 'diagnose_if' attribute on 'must_abs':
    4 |         __attribute__((diagnose_if(a >= 0, "Redundant abs call", "error")));
      |                        ^           ~~~~~~
1 warning and 1 error generated.
```
