這選項是用來加速浮點運算的，不過這也是有代價的，他不遵守IEEE和ISO的浮點運算標準，所以在追求精準度的地方不適用，使用`-ffast-math`時同時要開啟Optimization．

以下是一個範例
``` c
double x6(double num) {
    return num * num * num * num * num * num;
}
```
如果未加入`-ffast-math`時，編譯出來的assembly code是
``` asm
x6:
	push rbp
	mov rbp, rsp
	movsd qword ptr [rbp - 8], xmm0
	movsd xmm0, qword ptr [rbp - 8]
	mulsd xmm0, qword ptr [rbp - 8]
	mulsd xmm0, qword ptr [rbp - 8]
	mulsd xmm0, qword ptr [rbp - 8]
	mulsd xmm0, qword ptr [rbp - 8]
	mulsd xmm0, qword ptr [rbp - 8]
	pop rbp
	ret
```
但如果加入了`-ffast-math -O1`之後
``` asm
x6:
	mulsd xmm0, xmm0
	movapd xmm1, xmm0
	mulsd xmm1, xmm0
	mulsd xmm0, xmm1
	ret
```
可以看出指令變少了，所對應的代價就是精準度的變化，僅適用於對精準度不敏感的應用

#### About MSVC
MSVC也有類似於`-ffast-math`的功能，`/fp:fast`，同樣的也要開啟最佳化`/O1`

#### Documnet
以下文章討論到fastmath的一些corner cases，如果你遇到這些問題，斟酌使用
[Optimizations enabled by -ffast-math](https://kristerw.github.io/2021/10/19/fast-math/)
[Beware of fast-math](https://simonbyrne.github.io/notes/fastmath/#title)
[Towards Useful Fast-Math](https://llvm.org/devmtg/2024-10/slides/techtalk/Kaylor-Towards-Useful-Fast-Math.pdf)

