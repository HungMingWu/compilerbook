Clang Thread Safety Analysis is a C++ language extension，透過attribute的方式，幫助clang做靜態分析，`mutex.h`的內容可以從[網站]((https://clang.llvm.org/docs/ThreadSafetyAnalysis.html#mutexheader)上找到，這邊只貼上範例程式
``` cpp
#include "mutex.h"

class BankAccount {
private:
  Mutex mu;
  int   balance GUARDED_BY(mu);

  void depositImpl(int amount) {
    balance += amount;       // WARNING! Cannot write balance without locking mu.
  }

  void withdrawImpl(int amount) REQUIRES(mu) {
    balance -= amount;       // OK. Caller must have locked mu.
  }

public:
  void withdraw(int amount) {
    mu.Lock();
    withdrawImpl(amount);    // OK.  We've locked mu.
  }                          // WARNING!  Failed to unlock mu.

  void transferFrom(BankAccount& b, int amount) {
    mu.Lock();
    b.withdrawImpl(amount);  // WARNING!  Calling withdrawImpl() requires locking b.mu.
    depositImpl(amount);     // OK.  depositImpl() has no requirements.
    mu.Unlock();
  }
};
```
用clang加上`-Wthread-safety`編譯
``` bash
$ clang -c -Wthread-safety test.cpp
test.cpp:9:5: warning: writing variable 'balance' requires holding mutex 'mu' exclusively [-Wthread-safety-analysis]
    9 |     balance += amount;       // WARNING! Cannot write balance without locking mu.
      |     ^
test.cpp:20:3: warning: mutex 'mu' is still held at the end of function [-Wthread-safety-analysis]
   20 |   }                          // WARNING!  Failed to unlock mu.
      |   ^
test.cpp:18:8: note: mutex acquired here
   18 |     mu.Lock();
      |        ^
test.cpp:24:7: warning: calling function 'withdrawImpl' requires holding mutex 'b.mu' exclusively [-Wthread-safety-precise]
   24 |     b.withdrawImpl(amount);  // WARNING!  Calling withdrawImpl() requires locking b.mu.
      |       ^
test.cpp:24:7: note: found near match 'mu'
3 warnings generated.
```
跳出了Warning
簡單看一下`mutex.h`的部分
``` cpp
#if defined(__clang__) && (!defined(SWIG))
#define THREAD_ANNOTATION_ATTRIBUTE__(x)   __attribute__((x))
#else
#define THREAD_ANNOTATION_ATTRIBUTE__(x)   // no-op
#endif

#define GUARDED_BY(x) \
  THREAD_ANNOTATION_ATTRIBUTE__(guarded_by(x))
```
可以看出，這只對clang有用，因此可以直接加入code裡面而不會有問題，不過用新的attribute語法會更好
更詳細的教學可以參考[官方文件](https://clang.llvm.org/docs/ThreadSafetyAnalysis.html)
