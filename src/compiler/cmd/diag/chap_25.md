#### `-Wimplicit-fallthrough`
檢查出switch中可能的問題，可能要加上fallthrought或是修正邏輯
``` c
int main(int argc, char **argv) {
    switch (argc) {
        case 0:
            argc = 1;
        case 1:
            argc = 2;
    };
}
```
``` bash
$ clang test.c -c -Wimplicit-fallthrough
test.c:5:9: warning: unannotated fall-through between switch labels [-Wimplicit-fallthrough]
    5 |         case 1:
      |         ^
test.c:5:9: note: insert '__attribute__((fallthrough));' to silence this warning
    5 |         case 1:
      |         ^
      |         __attribute__((fallthrough));
test.c:5:9: note: insert 'break;' to avoid fall-through
    5 |         case 1:
      |         ^
      |         break;
```
#### `-Wswitch`
用來檢測`enum class`在switch case是否覆蓋了所有可能
``` cpp
enum class Color {
        Red,
        Green,
        Blue
};
int main(int argc, char **argv) {
        Color c;
        switch (c) {
        case Color::Red:
        case Color::Green:
                argc = 1;
        }
}
```
``` bash
$ g++ test.cpp -c -Wswitch
test.cpp: In function ‘int main(int, char**)’:
test.cpp:8:16: warning: enumeration value ‘Blue’ not handled in switch [-Wswitch]
    8 |         switch (c) {
      |              
```

#### `-Wswitch-default`
顧名思義，用來檢測是否涵蓋了default case
``` c
int main()
{
        int i = 0;
        switch (i) {
                case 0:
                        break;
                case 1:
                        break;
        }
}
```
``` bash
$ clang test.c -c -Wswitch-default
test.c:4:2: warning: 'switch' missing 'default' label [-Wswitch-default]
    4 |         switch (i) {
      |         ^
1 warning generated.

```
