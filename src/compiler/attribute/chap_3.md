最常用的attribute之一，相當於其他語言中的`defer`
不過問題是，這也是非標準，所以不用期望MSVC會支持
``` c
# include <stdio.h>

void clean_up(int *final_value)
{
	printf("Cleaning up\n");
	printf("Final value: %d\n",*final_value);
}

int main(int argc, char **argv)
{
	int avar [[gnu::cleanup(clean_up)]] = 1;
	avar = 5;
	return 0;
}
```
編譯並執行
``` bash
$ gcc test.cpp -o test -std=c23
$ ./test
Cleaning up
Final value: 5
```
