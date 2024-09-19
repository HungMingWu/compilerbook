將原先的程式改成這樣
``` cpp
include <cmath>
#include <iostream>
#include <string>
#include "config.h"

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
        const double outputValue = sqrt(inputValue);
        std::cout << "The square root of " << inputValue << " is " << outputValue
                << std::endl;
        return 0;
}
```
這邊我們要做的是動態生成`config.h`，因此要先提供一個Template `config.h.in`
``` bash
#pragma once

#define PROJECT_VERSION_MAJOR @PROJECT_VERSION_MAJOR@
#define PROJECT_VERSION_MINOR @PROJECT_VERSION_MINOR@
#define AUTHOR_NAME "@AUTHOR_NAME@"
```
之後再CMakeLists.txt當中提供對應的資料
``` bash
# Define configuration variables
set(PROJECT_VERSION_MAJOR 1)
set(PROJECT_VERSION_MINOR 0)
set(AUTHOR_NAME "HungMing")
```
接著透過`configure_file`來生成`config.h`
``` bash
configure_file(config.h.in config.h)
```
這個`config.h`會放置在`CMAKE_BINARY_DIR`底下，所以我們需要額外指定在哪個目錄向
而加入`CMAKE_BINARY_DIR`到Include Path有兩種方式
``` bash
target_include_directories(tutorial PRIVATE "${CMAKE_BINARY_DIR}")
```
這種方式在新版的CMake已不推薦，因此改用FILE_SET來寫，FILE_SET也可以支援Modules，這是`target_include_directories`辦不到的
``` bash
target_sources(tutorial
  PRIVATE
    tutorial.cxx
  PRIVATE
    FILE_SET generate_config
    TYPE HEADERS
    BASE_DIRS ${CMAKE_BINARY_DIR}
    FILES ${CMAKE_BINARY_DIR}/config.h
)
```
完整的CMakeLists.txt是這樣
``` bash
cmake_minimum_required(VERSION 3.28)
project(Tutorial)

# Define configuration variables
set(PROJECT_VERSION_MAJOR 1)
set(PROJECT_VERSION_MINOR 0)
set(AUTHOR_NAME "HungMing")

configure_file(config.h.in config.h)
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
```
