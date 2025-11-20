使用C++的人都知道Virtual Table是什麼東西，這裡就不多加解釋了

以下是我們的範例程式
``` cpp
class Shape {
public:
        virtual ~Shape() = default;
        virtual void draw() = 0;
};
int main()
{
        return 0;
}
```
照慣例，程式碼本身沒什麼技術含量
#### gcc
```  bash
$ g++ test.cpp -fdump-lang-class=text.txt
```
印出來的輸出大概像這樣
``` bash
Vtable for Shape
Shape::_ZTV5Shape: 5 entries
0     (int (*)(...))0
8     (int (*)(...))(& _ZTI5Shape)
16    0
24    0
32    (int (*)(...))__cxa_pure_virtual

Class Shape
   size=8 align=8
   base size=8 base align=8
Shape (0x0x770167dd5000) 0 nearly-empty
    vptr=((& Shape::_ZTV5Shape) + 16)
```
#### clang
Clang得到的輸出反而最沒用，不過還是紀錄一下
``` bash
$ clang -cc1 -fdump-record-layouts test.cpp > test.txt
```

``` bash
*** Dumping AST Record Layout
         0 | class Shape
         0 |   (Shape vtable pointer)
           | [sizeof=8, dsize=8, align=8,
           |  nvsize=8, nvalign=8]

```

#### msvc
``` powershell
cl.exe test.cpp /d1reportAllClassLayout > test.txt
```
可以得到類似這樣的輸出
``` powershell
class Shape	size(4):
	+---
 0	| {vfptr}
	+---

Shape::$vftable@:
	| &Shape_meta
	|  0
 0	| &Shape::{dtor} 
 1	| &Shape::draw 

Shape::{dtor} this adjustor: 0
Shape::draw this adjustor: 0
Shape::__delDtor this adjustor: 0
Shape::__vecDelDtor this adjustor: 0
```
#### One more thing
gdb也可以印出virtual table和structure layout
不過必須是一個expression，也就是需要產生一個真實的物件
改寫上面的程式碼
``` cpp
class Shape {
        int v;
public:
        virtual ~Shape() = default;
        virtual void draw() = 0;
};
class Circle : public Shape {
public:
        void draw() override {}
};
int main()
{
        Circle c;
        Shape *ptr = &c;
        return 0;
}

```
在跑到這行後
``` cpp
        Shape *ptr = &c;
```
在gdb下
``` bash
(gdb) info vtbl ptr
vtable for 'Shape' @ 0x402018 (subobject @ 0x7fffffffe150):
[0]: 0x4011ba <Circle::~Circle()>
[1]: 0x4011e2 <Circle::~Circle()>
[2]: 0x40116a <Circle::draw()>
```
可以看到類似的結果
而
``` bash
(gdb) ptype /o ptr
type = class Shape {
/*      8      |       4 */    int v;
/* XXX  4-byte padding   */

                               /* total size (bytes):   16 */
                             } *

```
可以印出我們的structure layout
``

#### Reference
[Dump C/C++ vtable & record layout information (clang + msvc + gcc)](https://gist.github.com/GavinRay97/700ff1631d7e5ac460efd0780759c908)
