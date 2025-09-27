gcc cross compile是一件很麻煩的事情
首先，你必須要有Cross compiler，如底下的`arm-linux-gnueabihf-gcc
接下來，你必須有`Target Platform`的Include header和Library，不然他會直接連結到Host環境的Library
``` bash
$ arm-linux-gnueabihf-gcc \
    --sysroot=/opt/rpi/sysroot \
    -I/opt/rpi/sysroot/usr/include \
    -L/opt/rpi/sysroot/usr/lib \
    -o hello-arm hello.c
```
#### Makefile
以下是一個最簡單的Makefile，實際使用比這複雜多了
```
CROSS_COMPILE   := arm-linux-gnueabihf-

CC              := $(CROSS_COMPILE)gcc
STRIP           := $(CROSS_COMPILE)strip

SYSROOT         := /opt/rpi/sysroot

CFLAGS          := --sysroot=$(SYSROOT) -I$(SYSROOT)/usr/include -O2
LDFLAGS         := --sysroot=$(SYSROOT) -L$(SYSROOT)/usr/lib

TARGET          := hello-arm
SRC             := hello.c

all: $(TARGET)

$(TARGET): $(SRC)
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $<
	$(STRIP) $@

clean:
	rm -f $(TARGET)
```
#### CMake
而這個是CMake的範例
``` bash
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR arm)

set(CMAKE_C_COMPILER   arm-linux-gnueabihf-gcc)
set(CMAKE_CXX_COMPILER arm-linux-gnueabihf-g++)

set(CMAKE_SYSROOT /opt/rpi/sysroot)

set(CMAKE_FIND_ROOT_PATH /opt/rpi/sysroot)

set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)
```
後面四行告訴CMake優先考慮Sysroot
