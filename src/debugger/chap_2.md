在上一節的方法雖然有用，不過仍有一些限制
	- 必須在整個過程完成之後才能使用，例如生成executable file之後
	- 無法平行處理
	- 檔案大小限制，常見的Linker會對2GB以上的Binary File作警告
因此Debug fission這方法油然而生

#### How to Use
``` bash
$ g++ -g -gsplit-dwarf main.cpp add.cpp -o main
```
clang的使用方式相同
此時你就可以在目錄下找到`main-main.dwo`和`main-add.dwo`兩個盪按，這就分別對應`main.o`和`add.o`的Debug Symbol

不過Package的部分尚未穩定，還無法在我的環境下正確運行，目前的情況還是上一節的方法比較保險
#### Reference
- [Separating debug symbols from executable](https://www.tweag.io/blog/2023-11-23-debug-fission/)
- [Debug fission - FOSDEM 2025](https://archive.fosdem.org/2025/events/attachments/fosdem-2025-5089-debug-fission-separating-debug-symbols-from-executables/slides/238310/Debug_fis_gZQOoQa.pdf)

