fuzzer test的用途和詳細使用這邊部細說了，這邊講的是該如何使用
目前這功能clang完全支持，MSVC部分支持，gcc不支援
照慣例，以下是測試程式，沒有什麼意義
``` cpp
#include <stddef.h>
#include <stdint.h>

void test_func(const uint8_t* data, size_t size)
{
        int a = 0;
        if (data[size + 1]) {
                a = 1;
        }
}

extern "C" int LLVMFuzzerTestOneInput(const uint8_t* data, size_t size)
{
        test_func(data, size);
        return 0;
}
```
不需要main這個進入點了，只需要實作`LLVMFuzzerTestOneInput`這個函示就可以了
clang的使用方式如下
``` bash
$ clang -g -O0 -fsanitize=fuzzer,address,undefined -o fuzzer fuzzer.cpp
```
經過測試，fuzzer必須和address, undefined sanitizer同時使用，不然容易偵測步道錯誤，最佳化的程度也會影響，如果需要fuzzer的話，希望將其降低
而MSVC的使用方式
``` powershell
$ >cl fuzzer.cpp /fsanitize=fuzzer
```
輸出的結果類似這樣
``` bash
$ ./fuzzer
INFO: Running with entropic power schedule (0xFF, 100).
INFO: Seed: 294583293
INFO: Loaded 1 modules   (8 inline 8-bit counters): 8 [0x55ae8bde0020, 0x55ae8bde0028),
INFO: Loaded 1 PC tables (8 PCs): 8 [0x55ae8bde0028,0x55ae8bde00a8),
INFO: -max_len is not provided; libFuzzer will not generate inputs larger than 4096 bytes
=================================================================
==2083==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x76e5261e0091 at pc 0x55ae8bd797fa bp 0x7ffdfc26f180 sp 0x7ffdfc26f178
READ of size 1 at 0x76e5261e0091 thread T0
    #0 0x55ae8bd797f9 in test_func(unsigned char const*, unsigned long) /fuzzer.cpp:7:6
    #1 0x55ae8bd798a8 in LLVMFuzzerTestOneInput /fuzzer.cpp:14:2
    #2 0x55ae8bc085f4 in fuzzer::Fuzzer::ExecuteCallback(unsigned char const*, unsigned long) /clang/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:619:13
    #3 0x55ae8bc0995c in fuzzer::Fuzzer::ReadAndExecuteSeedCorpora(std::__Fuzzer::vector<fuzzer::SizedFile, std::__Fuzzer::allocator<fuzzer::SizedFile>>&) /clang/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:812:3
    #4 0x55ae8bc09ff8 in fuzzer::Fuzzer::Loop(std::__Fuzzer::vector<fuzzer::SizedFile, std::__Fuzzer::allocator<fuzzer::SizedFile>>&) /clang/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:872:3
    #5 0x55ae8bc00a25 in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) /clang/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:915:6
    #6 0x55ae8bc1d0c2 in main /clang/compiler-rt/lib/fuzzer/FuzzerMain.cpp:20:10
    #7 0x7ac526e2a1c9 in __libc_start_call_main csu/../sysdeps/nptl/libc_start_call_main.h:58:16
    #8 0x7ac526e2a28a in __libc_start_main csu/../csu/libc-start.c:360:3
    #9 0x55ae8bbf7c74 in _start (/fuzzer+0x3cc74)

0x76e5261e0091 is located 0 bytes after 1-byte region [0x76e5261e0090,0x76e5261e0091)
allocated by thread T0 here:
    #0 0x55ae8bd35fc4 in malloc /clang/compiler-rt/lib/asan/asan_malloc_linux.cpp:67:3
    #1 0x55ae8bd79973 in operator new(unsigned long) cxa_noexception.cpp
    #2 0x55ae8bc0995c in fuzzer::Fuzzer::ReadAndExecuteSeedCorpora(std::__Fuzzer::vector<fuzzer::SizedFile, std::__Fuzzer::allocator<fuzzer::SizedFile>>&) /clang/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:812:3
    #3 0x55ae8bc09ff8 in fuzzer::Fuzzer::Loop(std::__Fuzzer::vector<fuzzer::SizedFile, std::__Fuzzer::allocator<fuzzer::SizedFile>>&) /clang/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:872:3
    #4 0x55ae8bc00a25 in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) /clang/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:915:6
    #5 0x55ae8bc1d0c2 in main /clang/compiler-rt/lib/fuzzer/FuzzerMain.cpp:20:10
    #6 0x7ac526e2a1c9 in __libc_start_call_main csu/../sysdeps/nptl/libc_start_call_main.h:58:16
    #7 0x7ac526e2a28a in __libc_start_main csu/../csu/libc-start.c:360:3
    #8 0x55ae8bbf7c74 in _start (/fuzzer+0x3cc74)

SUMMARY: AddressSanitizer: heap-buffer-overflow /fuzzer.cpp:7:6 in test_func(unsigned char const*, unsigned long)
Shadow bytes around the buggy address:
  0x76e5261dfe00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x76e5261dfe80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x76e5261dff00: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x76e5261dff80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x76e5261e0000: fa fa 00 00 fa fa 00 fa fa fa 00 fa fa fa 00 fa
=>0x76e5261e0080: fa fa[01]fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x76e5261e0100: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x76e5261e0180: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x76e5261e0200: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x76e5261e0280: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x76e5261e0300: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
==2083==ABORTING
MS: 0 ; base unit: 0000000000000000000000000000000000000000
```

雖然功能上比不上afl++，不過易用性和CI/CD的整合非常的好

#### Integrate to CMake
整合到CMake也不是多難的事情
``` cmake
add_executable(fuzzer fuzzer.cpp)
target_compile_options(
    fuzzer
    PRIVATE
    -fsanitize=fuzzer,address,undefined
    -fno-sanitize-recover=address,undefined
    -g
    -O3
)
target_link_options(
    fuzzer
    PRIVATE
    -fsanitize=fuzzer,address,undefined
    -fno-sanitize-recover=address,undefined
)
```
