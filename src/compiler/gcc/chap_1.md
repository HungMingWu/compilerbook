spec是一個特殊的configure file，告訴gcc如何處理如何處理各個階段，例如Preprocessor，Linker等等．允許User能夠修改gcc的默認行為，而不必重新編譯Compilier

以下指令可以看到目前gcc的預設specs
``` bash
$ gcc -dumpspecs
*asm:
%{m16|m32:--32}  %{m16|m32|mx32:;:--64}  %{mx32:--x32}  %{msse2avx:%{!mavx:-msse2avx}}

*asm_debug:
%{g*:%{%:debug-level-gt(0):}} %{ffile-prefix-map=*:--debug-prefix-map %*} %{fdebug-prefix-map=*:--debug-prefix-map %*}

*asm_debug_option:
%{g*:%{%:debug-level-gt(0):%{%:dwarf-version-gt(4):--gdwarf-5 ;%:dwarf-version-gt(3):--gdwarf-4 ;%:dwarf-version-gt(2):--gdwarf-3 ;:--gdwarf2 }}}
// ignored
```
而我們可以也可以指定自己的spec來更改gcc的行為．以`musl`為例
``` bash
exec "${REALGCC:-gcc}" "$@" -specs "/home/musl_build/lib/musl-gcc.specs"
```
`musl-gcc.spec`的內容大致長這樣
``` bash
%rename cpp_options old_cpp_options

*cpp_options:
-nostdinc -isystem /home/musl_build/include -isystem include%s %(old_cpp_options)

*cc1:
%(cc1_cpu) -nostdinc -isystem /home/musl_build/include -isystem include%s

*link_libgcc:
-L/home/musl_build/lib -L .%s

*libgcc:
libgcc.a%s %:if-exists(libgcc_eh.a%s)

*startfile:
%{!shared: /home/musl_build/lib/Scrt1.o} /home/musl_build/lib/crti.o crtbeginS.o%s

*endfile:
crtendS.o%s /home/musl_build/lib/crtn.o

*link:
-dynamic-linker /lib/ld-musl-x86_64.so.1 -nostdlib %{shared:-shared} %{static:-static} %{rdynamic:-export-dynamic}

*esp_link:


*esp_options:


*esp_cpp_options:

```
如果需要知道如何寫spec．請參閱gcc document．而clang沒有類似的機制
