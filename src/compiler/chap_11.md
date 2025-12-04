一樣是從LWN看到的，以下是一個C11接受的語法
``` c
#include <stddef.h>

void baz(size_t, int b[static 2]);
int main() {
    int a[] = {1, 2, 3, 4};
    baz(4, a); // ok
    return 0;
}
```
這邊的語法有些古怪，不過這編表示
- 傳進去的參數不可為NULL
- 陣列長度至少為2
- 這是合法的C11語法

以下才是Extension
gcc支持以下語法(clang不支持)
``` c
void baz(size_t n, int b[static n]);
```

#### Check Flexible Array Member in a structure
gcc和clang都有`__counted_by__`這個attribute，不過目前clang可以抓到這個問題，gcc不行
貼上測試程式
``` c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct message_packet {
    unsigned int data_count;
    char buffer[] __attribute__((__counted_by__(data_count)));
};

int main() {
    unsigned int count = 10;
    size_t struct_size = sizeof(struct message_packet) + (count * sizeof(char));

    struct message_packet *p = malloc(struct_size);

    if (!p) {
        perror("malloc failed");
        return 1;
    }

    p->data_count = count;

    p->buffer[9] = 'Z';
    printf("Access p->buffer[9] successfully.\n");

    printf("Attempting to access p->buffer[10]...\n");
    p->buffer[10] = 'X';

    printf("Access p->buffer[10] completed.\n");

    free(p);
    return 0;
}
```
用`__counted_by__`修飾這個buffer跟data_count相關
``` bash
$ clang -O2 -fsanitize=array-bounds test.c -o test
$ ./test
Attempting to access p->buffer[10]...
test.c:27:8: runtime error: index 10 out of bounds for type 'char[] __counted_by(data_count)' (aka 'char[]')
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior test.c:27:8
Access p->buffer[10] completed.
```
雖然不加這個屬性，用`Address Sanitizer`也能抓出錯誤，不過這就是一個提示，幫助編譯器和靜態分析器能做更精準地分析
