C++20 Moudle雖然已經問世多年了，不過要取代傳統的header files還有很長的路要走，因此各家編譯器還是有一套加速的方法，叫做`Precompiled headers`，而CMake則是讓整個過程變成更簡單
例如原先的
``` cmake
cmake_minimum_required(VERSION 3.16)
project(MyProject)
set(HEADER_FILES
	src/header.h)
set(SOURCE_FILES
	src/main.cpp
	${HEADER_FILES}
	)
include_directories(src)
add_executable(MyProject ${SOURCE_FILES})
```
只要加上
``` cmake
target_precompile_headers(MyProject PRIVATE ${HEADER_FILES})
```
即可
