
gcc/clang有很多Optimization的選項
#### -O0
不開啟最佳化
#### -O or -O1
兩者是相等的，在不影響編譯速度的情況下，盡量開啟最佳化選項
#### -O2
跟`-O1`不同，會犧牲編譯速度，而提高執行時的運行速度
#### -O3
基於`-O2`的改進，會利用現代CPU的Pipeline和Cache等做最佳化
#### -Os
這個也是基於`-O2`的改進，不過這目標跟`-O3`不一樣，希望將binary size最小化
#### -Ofast
基於`-O3`的改進，不會嚴格遵守標準的最佳化，所以會開啟`-ffast-math`之類的選項
#### -Og
實現不影響Debug的Optimization
