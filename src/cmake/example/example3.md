我們將上個範例的`sqrt`抽出，使用另一個sqrt implemtation，大概像這樣

這是我們對外的API，使用者只會看到這個Header file
`MathFunctions/include/MathFunctions.h`
``` cpp
#pragma once

namespace mathfunctions {
        double sqrt(double x);
}
```
而接下來是我們實作的部分，都放在`src/`目錄底下
`MathFunctions/src/MathFunctions.cxx`
``` cpp
#include "MathFunctions.h"
#include "mysqrt.h"

namespace mathfunctions {
        double sqrt(double x) {
                return detail::sqrt(x);
        }
}
```
`MathFunctions/src/mysqrt.h`
``` cpp
#pragma once

namespace mathfunctions::detail {
        double sqrt(double x);
}
```
`MathFunctions/src/mysqrt.cxx`
``` cpp
#include "mysqrt.h"
#include <math.h>

namespace mathfunctions::detail {
        double sqrt(double x) {
                return ::sqrt(x);
        }
}
```
接著需要在`MathFunctions`目錄底下新增一個CMakeLists.txt，內容大概是這個樣子
``` bash
add_library(MathFunctions)
target_sources(MathFunctions
  PUBLIC
    FILE_SET export_headers
    TYPE HEADERS
    BASE_DIRS include/
    FILES include/MathFunctions.h
  PRIVATE
    src/MathFunctions.cxx
    src/mysqrt.h
    src/mysqrt.cxx
)

target_link_libraries(MathFunctions
  PRIVATE
    m
)
```
從內容我們可以知道
- 我們建立了一個`MathFunctions` Library
- 我們建立了一個FILE_SET `export_headers`，它的TYPE是Headers
	- 可以擁有一個以上的FILE_SET
- 我們對外部公開了`MathFunctions.h`
- 我們指定了`target_link_libraries` link `libm`做數學運算
- 剩下的implementation unit設成 PRIVATE
回頭修改我們的`tutorial.cxx`以及CMakeLists.txt
``` cpp
#include <cmath>
#include <iostream>
#include <string>
#include "config.h"
#include "MathFunctions.h"

int main(int argc, char* argv[])
{
        std::cout << "Project Version: " << PROJECT_VERSION_MAJOR << "." << PROJECT_VERSION_MINOR << std::endl;
        std::cout << "Author: " << AUTHOR_NAME << std::endl;

        if (argc < 2) {
                std::cout << "Usage: " << argv[0] << " number" << std::endl;
                return 1;
        }

        // convert input to double
        const double inputValue = atof(argv[1]);

        // calculate square root
        const double outputValue = mathfunctions::sqrt(inputValue);
        std::cout << "The square root of " << inputValue << " is " << outputValue
                << std::endl;
        return 0;
}
```
``` bash
cmake_minimum_required(VERSION 3.28)
project(Tutorial)

# Define configuration variables
set(PROJECT_VERSION_MAJOR 1)
set(PROJECT_VERSION_MINOR 0)
set(AUTHOR_NAME "HungMing")

configure_file(config.h.in config.h)

add_subdirectory(MathFunctions)

add_executable(tutorial)

target_sources(tutorial
  PRIVATE
    tutorial.cxx
  PRIVATE
    FILE_SET generate_config
    TYPE HEADERS
    BASE_DIRS ${CMAKE_BINARY_DIR}
    FILES ${CMAKE_BINARY_DIR}/config.h
)

target_link_libraries(tutorial
  PRIVATE
    MathFunctions
)
```
Code的部分沒什麼好說的，這邊提的是
- 使用add_subdirectory，將MathFunction當作sub project
- 使用target_link_libraries連結MathFunction，由於MathFunction的HEADER FILE_SET是PUBLIC，所以使用這Library的Consumer可以正確找到inclusion path，而不用額外指定header file search path

