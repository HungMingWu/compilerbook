要整合編譯器中的Sanitizers，就類似於static analyzer的方式就可以了
``` bash
target_compile_options(<target>
PRIVATE
        $<$<CXX_COMPILER_ID:MSVC>:/fsanitize=address>
        $<$<CXX_COMPILER_ID:GNU,Clang>:-fsanitize=address>
)
target_link_options(<target>
PRIVATE
        $<$<CXX_COMPILER_ID:MSVC>:/fsanitize=address>
        $<$<CXX_COMPILER_ID:GNU,Clang>:-fsanitize=address>
)
```
