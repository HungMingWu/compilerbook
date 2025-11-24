使用C++的人都知道Virtual Table是什麼東西，這裡就不多加解釋了

以下是我們的範例程式
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
   size=16 align=8
   base size=12 base align=8
Shape (0x0x7e7be27d5000) 0
    vptr=((& Shape::_ZTV5Shape) + 16)

Vtable for Circle
Circle::_ZTV6Circle: 5 entries
0     (int (*)(...))0
8     (int (*)(...))(& _ZTI6Circle)
16    (int (*)(...))Circle::~Circle
24    (int (*)(...))Circle::~Circle
32    (int (*)(...))Circle::draw

Class Circle
   size=16 align=8
   base size=12 base align=8
Circle (0x0x7e7be260e270) 0
    vptr=((& Circle::_ZTV6Circle) + 16)
Shape (0x0x7e7be27d5120) 0
      primary-for Circle (0x0x7e7be260e270)

```
#### clang
Clang也能輸出virtual table的 Layout，使用以下方式
``` bash
$ clang -Xclang -fdump-vtable-layouts test.cpp
```
輸出的結果類似這樣
``` bash
Original map
 void Circle::draw() -> void Shape::draw()
 Circle::~Circle() -> Shape::~Shape()
Vtable for 'Circle' (5 entries).
   0 | offset_to_top (0)
   1 | Circle RTTI
       -- (Circle, 0) vtable address --
       -- (Shape, 0) vtable address --
   2 | Circle::~Circle() [complete]
   3 | Circle::~Circle() [deleting]
   4 | void Circle::draw()

VTable indices for 'Circle' (3 entries).
   0 | Circle::~Circle() [complete]
   1 | Circle::~Circle() [deleting]
   2 | void Circle::draw()

Original map
 void Circle::draw() -> void Shape::draw()
 Circle::~Circle() -> Shape::~Shape()
Vtable for 'Shape' (5 entries).
   0 | offset_to_top (0)
   1 | Shape RTTI
       -- (Shape, 0) vtable address --
   2 | Shape::~Shape() [complete]
   3 | Shape::~Shape() [deleting]
   4 | void Shape::draw() [pure]

VTable indices for 'Shape' (3 entries).
   0 | Shape::~Shape() [complete]
   1 | Shape::~Shape() [deleting]
   2 | void Shape::draw()

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
#### Reference
[Dump C/C++ vtable & record layout information (clang + msvc + gcc)](https://gist.github.com/GavinRay97/700ff1631d7e5ac460efd0780759c908)
