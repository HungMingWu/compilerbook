關於MSVC的c++ library，有幾個地方可以加強安全性
- `_ITERATOR_DEBUG_LEVEL`：可以控制C++ STL iterator的檢查，預設在Deubg模式下將`_ITERATOR_DEBUG_LEVEL`設成2
-  從VS2022 17.14之後，實做了 C++26 STL Hardening，所以將`_MSVC_STL_HARDENING`定義成1之後就能得到l另兩家hardening的效果
