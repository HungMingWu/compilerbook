假設我們要寫Unit test，想要測試 class裡面的private function或是private member要怎麼做

#### Method 1 Preprocessor's way
直接將private定義成public
``` cpp
#define private public
class Obj {
private:
        int value;
};

int main() {
        Obj o;
        o.value = 1;
}
```
雖然有用，不過副作用也是超大，破壞了所有封裝
不過MSVC只支持這種方式
#### Method 2: `-fno-access-control`
把上面那行`#define`拿掉，在編譯的時候加上即可
``` bash
$ clang test.cpp -o test -fno-access-control
```

