現在CMake支持CUDA就是這麼簡單
``` bash
cmake_minimum_required(VERSION 3.18)
project(demo CXX CUDA)
find_package(CUDAToolkit REQUIRED)

add_executable(demo)

target_sources(demo
  PRIVATE
      demo.cpp
      demo.cu
)
```
酸然遇到CUDA還有很多細節的部分，例如會碰到`CMAKE_CUDA_ARCHITECTURES`未設定的
部分，那時候在此為基礎增加就可以了

