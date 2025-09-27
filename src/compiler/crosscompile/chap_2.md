基於上一章gcc的內容，clang不需要cross compiler，一套compiler走天下，跟gcc相同的是，你還是需要`sysroot`的存在
``` bash
clang --target=arm-linux-gnueabihf \
      --sysroot=/opt/rpi/sysroot \
      -I/opt/rpi/sysroot/usr/include \
      -L/opt/rpi/sysroot/usr/lib \
      hello.c -o hello-arm
```
取而代之的，是`--target=....`告訴clang backend要產生怎樣的code

#### Makefile
``` bash
CC              := clang
STRIP           := llvm-strip

TARGET_TRIPLE   := arm-linux-gnueabihf

SYSROOT         := /opt/rpi/sysroot

CFLAGS          := --target=$(TARGET_TRIPLE) --sysroot=$(SYSROOT) -O2 \
                   -I$(SYSROOT)/usr/include
LDFLAGS         := --target=$(TARGET_TRIPLE) --sysroot=$(SYSROOT) \
                   -L$(SYSROOT)/usr/lib -lc -lm -lgcc

TARGET          := hello-arm
SRC             := hello.c

all: $(TARGET)

$(TARGET): $(SRC)
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $<
	$(STRIP) $@

clean:
	rm -f $(TARGET)
```
跟gcc的版本大同小異
#### CMake
``` bash
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR arm)

set(CMAKE_C_COMPILER clang)
set(CMAKE_CXX_COMPILER clang++)

set(TARGET_TRIPLE arm-linux-gnueabihf)

set(CMAKE_SYSROOT /opt/rpi/sysroot)

set(CMAKE_C_FLAGS "--target=${TARGET_TRIPLE} --sysroot=${CMAKE_SYSROOT}" CACHE STRING "" FORCE)
set(CMAKE_CXX_FLAGS "--target=${TARGET_TRIPLE} --sysroot=${CMAKE_SYSROOT}" CACHE STRING "" FORCE)

set(CMAKE_FIND_ROOT_PATH ${CMAKE_SYSROOT})
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)
```
與gcc版本也差不多
