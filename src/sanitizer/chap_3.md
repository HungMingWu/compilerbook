Thread Sanitizer是在Runtime檢查Thread中有啥異常行為，如Data Race
現在只有gcc/clang有支持，MSVC是不支持的
以下是一個簡單的Race condition範例
``` cpp
#include <thread>
int a;
void test()
{
        a = 1;
}

int main(void)
{
        std::thread s(test);
        int b = a + 1;
        s.join();
        return b;
}
```
直接用gcc編譯
``` bash
$ g++ -g -fsanitize=thread test.cpp -o test
```
輸出類似
``` bash
==================
WARNING: ThreadSanitizer: data race (pid=18111)
  Read of size 4 at 0x566180e2001c by main thread:
    #0 main <null> (test+0x13aa) (BuildId: 8a593b0765721e683ab4da9323e60b6dfee0f500)

  Previous write of size 4 at 0x566180e2001c by thread T1:
    #0 test() <null> (test+0x134b) (BuildId: 8a593b0765721e683ab4da9323e60b6dfee0f500)
    #1 void std::__invoke_impl<void, void (*)()>(std::__invoke_other, void (*&&)()) <null> (test+0x20be) (BuildId: 8a593b0765721e683ab4da9323e60b6dfee0f500)
    #2 std::__invoke_result<void (*)()>::type std::__invoke<void (*)()>(void (*&&)()) <null> (test+0x2013) (BuildId: 8a593b0765721e683ab4da9323e60b6dfee0f500)
    #3 void std::thread::_Invoker<std::tuple<void (*)()> >::_M_invoke<0ul>(std::_Index_tuple<0ul>) <null> (test+0x1f68) (BuildId: 8a593b0765721e683ab4da9323e60b6dfee0f500)
    #4 std::thread::_Invoker<std::tuple<void (*)()> >::operator()() <null> (test+0x1f0a) (BuildId: 8a593b0765721e683ab4da9323e60b6dfee0f500)
    #5 std::thread::_State_impl<std::thread::_Invoker<std::tuple<void (*)()> > >::_M_run() <null> (test+0x1ebc) (BuildId: 8a593b0765721e683ab4da9323e60b6dfee0f500)
    #6 <null> <null> (libstdc++.so.6+0xeabb3) (BuildId: 40b9b0d17fdeebfb57331304da2b7f85e1396ef2)

  Location is global 'a' of size 4 at 0x566180e2001c (test+0x501c)

  Thread T1 (tid=18113, finished) created by main thread at:
    #0 pthread_create ../../../../src/libsanitizer/tsan/tsan_interceptors_posix.cpp:1022 (libtsan.so.2+0x5a267) (BuildId: 64c1e8de04b11a7d960abd7e45f94f3b277b7779)
    #1 std::thread::_M_start_thread(std::unique_ptr<std::thread::_State, std::default_delete<std::thread::_State> >, void (*)()) <null> (libstdc++.so.6+0xeac88) (BuildId: 40b9b0d17fdeebfb57331304da2b7f85e1396ef2)
    #2 main <null> (test+0x139b) (BuildId: 8a593b0765721e683ab4da9323e60b6dfee0f500)

SUMMARY: ThreadSanitizer: data race (/home/test+0x13aa) (BuildId: 8a593b0765721e683ab4da9323e60b6dfee0f500) in main
==================
ThreadSanitizer: reported 1 warnings
```
至於如何分析，解決問題，需要另外的知識背景了
clang還有額外的compile time thread safty analysis，請參考clang的部分
