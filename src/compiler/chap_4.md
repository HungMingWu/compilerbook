雖然這是大部分的人都知道的東西，不過這仍然不是C/C++標準的一部分，只不過三大Compiler都實做了這功能
這95%等同於常見的`include guard`
``` c
#ifndef _XXXX_INCLUDED_
#define _XXXX_INCLUDED_
#endif
```
相似，卻又不完全相同
- `#prgrama once`在編譯每個Compile Unit時，保證只載入檔案一次，而`include guard`則是每次都載入，然後只保留一份，其他都捨棄掉，這很浪費計算資源
- 不過假設有多個檔案內容完全相同，`#pragrma once`雖然只載速檔案一次，不過重複內容的檔案都被視為獨立的個體，還是會遇到multiple definition的問題，而`include guard`則不會

選擇就視情況而定，不過95%的情況用`#pragma once`就行了
