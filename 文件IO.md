# .文件IO

## 1、Linux下文件类型

普通文件(regular):存在于外部存储器中，用于存储普通数据。

目录文件(directory):用于存放目录项，是文件系统管理的重要文件类型。

管道文件(pipe):一种用于进程间通信的特殊文件，也称为命名管道FIFO。

套接字文件(socket):一种用于网络间通信的特殊文件。

链接文件(link):用于间接访问另外一个目标文件，相当于 Windows 快捷方式

字符设备文件(character):字符设备在应用层的访问接口。

块设备文件(block):块设备在应用层的访问接口。



```
系统 I/O（Input/Output）和标准 I/O 是两种不同的 I/O 操作方式，它们有以下区别：

系统 I/O（System I/O）：

系统 I/O 是直接与操作系统进行交互的方式，通常通过系统调用（system calls）来实现。
典型的系统 I/O 包括使用 open、read、write、close、ioctl 等系统调用来打开、读取、写入和关闭文件、设备等。
系统 I/O 提供了更底层、更直接的控制能力，但通常需要更多的代码来处理错误和边界情况。
标准 I/O（Standard I/O）：

标准 I/O 是建立在系统 I/O 基础之上的高级 I/O 操作方式，提供了更友好、更方便的 API。
典型的标准 I/O 包括使用 stdio.h 中的函数，如 fopen、fclose、fread、fwrite 等来打开、关闭、读取和写入文件。
标准 I/O 提供了缓冲、格式化输入输出、错误处理等功能，使得编程更简单、更易于理解。
```



## 2、标准IO

- 由标准C库提供函数接口，特点就是特别丰富
- 有提供缓冲区，对于海量数据的处理效率较高
- 编程的过程中建议使用标准IO来实现，除非不能用
- 目录操作
  如何读取目录的内容库文件
- 库的移植
  参数化动态链接库

1、fopen 函数是标准 C 库中用于打开文件的函数，其原型为：

```
FILE *fopen(const char *path, const char *mode);
path 参数是一个字符串，表示要打开的文件的路径名。
mode 参数也是一个字符串，表示打开文件的模式。常见的模式包括：
"r"：只读模式，打开文件用于读取。文件必须存在，否则打开失败。
"w"：写入模式，打开文件用于写入。如果文件不存在，则创建文件；如果文件已存在，则先清空文件内容。
"a"：追加模式，打开文件用于写入，写入的数据追加到文件末尾。如果文件不存在，则创建文件。
"r+"：读写模式，打开文件用于读取和写入，文件必须存在。
"w+"：读写模式，打开文件用于读取和写入。如果文件不存在，则创建文件；如果文件已存在，则先清空文件内容。
"a+"：读写模式，打开文件用于读取和追加写入，写入的数据追加到文件末尾。如果文件不存在，则创建文件。
fopen 函数返回一个指向 FILE 类型的指针，该指针用于后续对文件进行读写操作。如果打开文件失败，则返回 NULL。
例如，要以只读模式打开名为 "example.txt" 的文件，可以使用以下代码：

FILE *file = fopen("example.txt", "r");
if (file == NULL) {
    perror("Failed to open file");
    return -1;
}
在使用完文件后，应该使用 fclose 函数关闭文件，以释放资源。
```

2、`fwrite` 函数是标准 C 库中用于向文件中写入数据的函数，其原型为：

```
size_t fwrite(const void *ptr, size_t size, size_t nmemb, FILE *stream);
```

这个函数接受四个参数：

- `ptr`：指向要写入数据的缓冲区的指针。
- `size`：要写入的每个数据项的大小（以字节为单位）。
- `nmemb`：要写入的数据项的数量。
- `stream`：指向要写入的文件的 `FILE` 结构的指针。

`fwrite` 函数会将由 `ptr` 指向的数据写入到由 `stream` 指向的文件中。它返回成功写入的数据项的数量，如果出现错误或者达到了文件结尾，则返回一个小于 `nmemb` 的值。通常情况下，如果返回值与 `nmemb` 不相等，则可能发生了写入错误。

例如，要向文件中写入一个整数数组，可以使用以下代码：

```
#include <stdio.h>

int main() {
    FILE *file = fopen("data.txt", "w");
    if (file == NULL) {
        perror("Failed to open file");
        return 1;
    }

    int data[] = {1, 2, 3, 4, 5};
    size_t elements_written = fwrite(data, sizeof(int), 5, file);
    if (elements_written != 5) {
        perror("Error writing to file");
        fclose(file);
        return 1;
    }

    fclose(file);
    return 0;
}
```

在这个例子中，`fwrite` 函数将整数数组 `data` 写入到文件中，每个整数占用 `sizeof(int)` 个字节，共写入了 5 个整数。

3、`fseek` 函数是标准 C 库中用于设置文件流的位置指针位置的函数，其原型为：

```c
int fseek(FILE *stream, long offset, int whence);
```

这个函数接受三个参数：
- `stream`：指向要设置位置指针的文件流的 `FILE` 结构的指针。
- offset`：偏移量，即要设置的新位置相对于 `whence` 参数指定的位置的偏移量。
- `whence`：指定起始位置的标志，可以是 `SEEK_SET`、`SEEK_CUR` 或 `SEEK_END`。

`fseek` 函数将文件流的位置指针设置为由 `whence` 参数指定的位置加上 `offset` 字节的偏移量。具体而言：
- 如果 `whence` 是 `SEEK_SET`，则将位置指针设置为文件的起始位置加上 `offset` 字节的偏移量。
- 如果 `whence` 是 `SEEK_CUR`，则将位置指针设置为当前位置加上 `offset` 字节的偏移量。
- 如果 `whence` 是 `SEEK_END`，则将位置指针设置为文件末尾加上 `offset` 字节的偏移量。

`fseek` 函数的返回值为 0 表示成功，非 0 表示失败。

例如，要将文件位置指针设置到文件开头，可以使用以下代码：

```c
#include <stdio.h>

int main() {
    FILE *file = fopen("example.txt", "r");
    if (file == NULL) {
        perror("Failed to open file");
        return 1;
    }

    // 将文件位置指针设置到文件开头
    if (fseek(file, 0L, SEEK_SET) != 0) {
        perror("Error setting file position");
        fclose(file);
        return 1;
    }

    fclose(file);
    return 0;
}
```

在这个例子中，`fseek` 函数将文件位置指针设置到文件开头（偏移量为 0）。

4、`ftell` 函数用于获取文件流的当前位置指针相对于文件起始位置的偏移量（以字节为单位）。其原型为：

```c
long ftell(FILE *stream);
```

这个函数接受一个参数 `stream`，是指向要获取位置指针的文件流的 `FILE` 结构的指针。

`ftell` 函数返回一个 `long` 类型的值，表示当前位置指针相对于文件起始位置的偏移量。如果出错，返回值为 -1L。

例如，下面的代码演示了如何使用 `ftell` 函数获取文件位置指针的当前偏移量：

```c
#include <stdio.h>

int main() {
    FILE *file = fopen("example.txt", "r");
    if (file == NULL) {
        perror("Failed to open file");
        return 1;
    }

    // 获取当前文件位置指针的偏移量
    long offset = ftell(file);
    if (offset == -1L) {
        perror("Error getting file position");
        fclose(file);
        return 1;
    }

    printf("Current file position offset: %ld\n", offset);

    fclose(file);
    return 0;
}
```

在这个例子中，`ftell` 函数被调用来获取文件位置指针的当前偏移量，并将其打印出来。

5、`getchar()`、`fgetc(FILE * stream)` 和 `getc(FILE * stream)` 都是用于从文件流中读取一个字符的函数或宏。

1. `getchar()`：
   - `getchar()` 函数用于从标准输入文件（通常是键盘）中读取一个字符，并将其作为 `unsigned char` 类型的整数返回。
   - 它是标准库函数，包含在 `<stdio.h>` 头文件中。
   - 该函数在读取字符后，文件位置指针会自动向前移动，指向下一个字符位置。

2. `fgetc(FILE * stream)`：
   - `fgetc()` 函数用于从指定的文件流中读取一个字符，并将其作为 `unsigned char` 类型的整数返回。
   - 它是标准库函数，包含在 `<stdio.h>` 头文件中。
   - 参数 `stream` 是指向要读取字符的文件流的指针。
   - 该函数在读取字符后，文件位置指针会自动向前移动，指向下一个字符位置。

3. `getc(FILE * stream)`：
   - `getc()` 是一个宏定义，它在实现上与 `fgetc()` 函数完全相同。
   - 也用于从指定的文件流中读取一个字符，并将其作为 `unsigned char` 类型的整数返回。
   - 它是标准库宏，包含在 `<stdio.h>` 头文件中。
   - 参数 `stream` 是指向要读取字符的文件流的指针。
   - 该宏在读取字符后，文件位置指针会自动向前移动，指向下一个字符位置。

这些函数和宏的返回值为读取的字符的整数表示，如果到达文件末尾或出错，返回值为 `EOF`（End Of File）。`EOF` 是一个负整数常量，通常定义为 `-1`。

这些函数和宏的使用场景在于从文件中逐字符读取数据，而不同的是 `getchar()` 是用于标准输入流（键盘），而 `fgetc()` 和 `getc()` 可以用于任意文件流。

```
#include <stdio.h>

int main() {
    FILE *file;
    int ch;

    // 打开文件以供读取
    file = fopen("example.txt", "r");
    if (file == NULL) {
        perror("Error opening file");
        return 1;
    }

    // 使用 getchar() 从标准输入中读取字符，并打印出来
    printf("Reading from standard input (keyboard):\n");
    printf("Enter some text, press Enter, then Ctrl+D (Unix) or Ctrl+Z (Windows) to end:\n");
    while ((ch = getchar()) != EOF) {
        putchar(ch);
    }

    // 重新设置文件位置指针以从文件中读取
    fseek(file, 0, SEEK_SET);

    // 使用 fgetc() 从文件中读取字符，并打印出来
    printf("\n\nReading from file using fgetc():\n");
    while ((ch = fgetc(file)) != EOF) {
        putchar(ch);
    }

    // 重新设置文件位置指针以从文件中读取
    fseek(file, 0, SEEK_SET);

    // 使用 getc() 从文件中读取字符，并打印出来
    printf("\n\nReading from file using getc():\n");
    while ((ch = getc(file)) != EOF) {
        putchar(ch);
    }

    // 关闭文件
    fclose(file);

    return 0;
}

```

```
int putchar (int c); // 把一个字符写入到标准输出文件中
int putc(int c, FILE * stream); // 宏定义
int fputc(int c, FILE * stream); // 把 整型c转为字符并写入到 stream 所对应文件中

 返回值：
会返回写入成功的字符, 即参数 c. 若返回 EOF 则代表写入失败

#include <stdio.h>

int main() {
    FILE *file;
    int ch;

    // 打开文件以供写入
    file = fopen("output.txt", "w");
    if (file == NULL) {
        perror("Error opening file");
        return 1;
    }

    // 使用 putchar() 将字符写入到标准输出
    printf("Using putchar() to write characters to standard output:\n");
    putchar('H');
    putchar('e');
    putchar('l');
    putchar('l');
    putchar('o');
    putchar('\n');

    // 使用 putc() 将字符写入到文件中
    printf("\nUsing putc() to write characters to file:\n");
    putc('W', file);
    putc('o', file);
    putc('r', file);
    putc('l', file);
    putc('d', file);
    putc('\n', file);

    // 使用 fputc() 将字符写入到文件中
    printf("\nUsing fputc() to write characters to file:\n");
    fputc('1', file);
    fputc('2', file);
    fputc('3', file);
    fputc('4', file);
    fputc('5', file);
    fputc('\n', file);

    // 关闭文件
    fclose(file);

    return 0;
}

```

```
按行进行读取/写入到文件中
char * fgets(char * s, int size, FILE * stream);
char * gets(char *s); // 从标准输入中读取一个字符串

返回值：
若成功则返回 s 指针,
返回 NULL 则表示有错误发生.
注意：
fgets 读取文件时直到出现换行字符、读到文件尾或是已读了 size-1 个字符为止, 最后会加上
NULL 作为字符串结束
gets 参数中只有一个指针s ，因此在读取的时候会一直读取数据直到遇到换行符/文件末尾。没有
控制他的长度，需要注意指针s所申请内存区的大小，不要越界。

这两个函数都用于从输入流中读取字符串，但它们之间有一些重要的区别。

char *fgets(char *s, int size, FILE *stream)：

fgets() 函数从指定的文件流 stream 中读取一行文本，并将其存储到字符串 s 中。
它是标准库函数，包含在 <stdio.h> 头文件中。
参数 s 是用于存储读取的字符串的缓冲区。
参数 size 指定了缓冲区的大小，防止写入超出缓冲区大小的数据。
参数 stream 是要从中读取数据的文件流指针。
函数成功时返回指向 s 的指针，如果到达文件尾或发生错误，则返回 NULL。
char *gets(char *s)：

gets() 函数从标准输入流中读取一行文本，并将其存储到字符串 s 中。
它是标准库函数，包含在 <stdio.h> 头文件中。
参数 s 是用于存储读取的字符串的缓冲区。
函数成功时返回指向 s 的指针，如果到达文件尾或发生错误，则返回 NULL。
注意： gets() 函数存在严重的安全性问题，因为它不会检查输入字符串的长度，可能会导致缓冲区溢出。因此，它已经被废弃，不建议使用。
在实际编程中，应该尽量避免使用 gets() 函数，而是使用更安全的替代方法，如 fgets() 函数。下面是一个使用 fgets() 函数的简单示例：

#include <stdio.h>

int main() {
    char buffer[100];

    printf("Enter a string: ");
    fgets(buffer, sizeof(buffer), stdin); // 从标准输入中读取一行

    printf("You entered: %s", buffer);

    return 0;
}
这个程序会提示用户输入一个字符串，并使用 fgets() 函数读取输入的字符串，然后将其打印出来。
```

```
int fputs(const char * s, FILE * stream); // 把s所指向则内存写入到stream 所指向的文件中
int puts(const char *s); // 把一个字符串输出到标准输出文件中

返回值：若成功则返回写出的字符个数, 返回 EOF 则表示有错误发生
如何判断返回EOF时是出错还是到达文件末尾：
 int feof(FILE * stream); // 检查文件流是否读到了文件尾，
 //如果到达文件末尾则返回非零值

 int ferror(FILE *stream); // 检查文件流是否有错误发生
 // 如有错误发生则返回非 0 值

以上两个函数一般可以配合使用， 如果返回EOF 则可以先使用feof来判断是否到达文件末尾， 如果不
是则可以使用ferror 来判断是否出错了， 如果是出错再调用 strerror/perror 来获取出错信息。

#include <stdio.h>

int main() {
    FILE *file;
    const char *str = "Hello, world!";

    // 打开文件以供写入
    file = fopen("output.txt", "w");
    if (file == NULL) {
        perror("Error opening file");
        return 1;
    }

    // 使用 fputs() 将字符串写入到文件中
    fputs(str, file);

    // 关闭文件
    fclose(file);

    // 使用 puts() 输出字符串到标准输出
    puts(str);

    return 0;
}

```

```
输入函数：

int scanf(const char *format, ...)：从标准输入流中按照指定的格式 format 读取数据。可以使用变长参数列表来接收读取的数据。
int fscanf(FILE *stream, const char *format, ...)：从指定的文件流 stream 中按照指定的格式 format 读取数据。可以使用变长参数列表来接收读取的数据。
int sscanf(const char *str, const char *format, ...)：从指定的内存中的字符串 str 中按照指定的格式 format 读取数据。可以使用变长参数列表来接收读取的数据。
输出函数：

int printf(const char *format, ...)：向标准输出流中按照指定的格式 format 输出数据。
int fprintf(FILE *stream, const char *format, ...)：向指定的文件流 stream 中按照指定的格式 format 输出数据。
int dprintf(int fd, const char *format, ...)：向指定的文件描述符 fd 中按照指定的格式 format 输出数据。
int sprintf(char *str, const char *format, ...)：将按照指定的格式 format 格式化的数据写入到字符串 str 中。
int snprintf(char *str, size_t size, const char *format, ...)：将按照指定的格式 format 格式化的数据写入到字符串 str 中，但最多写入 size 个字符。
这些函数中的 format 参数是一个格式字符串，它指定了如何格式化输入或输出的数据，包括数据类型、宽度、精度等信息。格式字符串中的格式化指示符由 % 字符开始，后跟一个或多个格式说明符，如 %d、%f、%s 等。

这些函数返回成功写入或读取的项目数，或者在出错时返回负值。
```



```
按数据块读取
size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream);
size_t fwrite( void *ptr, size_t size, size_t nmemb, FILE *stream);
以上两个函数都是按数据块进行读写，size 为每一个数据块的大小， nmemb 为多少块数据

#include <stdio.h>

// 定义一个简单的结构体 Person
typedef struct {
    char name[50];
    int age;
} Person;

int main() {
    FILE *fp;
    Person people[2]; // 假设有两个人员

    // 写入数据到文件
    fp = fopen("people.bin", "wb");
    if (fp == NULL) {
        perror("Error opening file");
        return -1;
    }

    // 初始化人员数据
    strcpy(people[0].name, "Alice");
    people[0].age = 30;
    strcpy(people[1].name, "Bob");
    people[1].age = 25;

    // 将结构体数组写入到文件中
    fwrite(people, sizeof(Person), 2, fp);

    // 关闭文件
    fclose(fp);

    // 从文件中读取数据
    fp = fopen("people.bin", "rb");
    if (fp == NULL) {
        perror("Error opening file");
        return -1;
    }

    // 读取数据到新的数组中
    Person people_read[2];
    fread(people_read, sizeof(Person), 2, fp);

    // 输出读取到的数据
    for (int i = 0; i < 2; ++i) {
        printf("Person %d: Name = %s, Age = %d\n", i+1, people_read[i].name, people_read[i].age);
    }

    // 关闭文件
    fclose(fp);

    return 0;
}

```



## 3、系统IO

- 由**系统**直接提供的函数接口，特点就是特别简介，功能单一
- 没有设置缓冲区，因此对于海量数据效率较低
- 套接字文件以及设备文件只能使用系统IO来访问

打开文件 

open

```
定义函数 ：
 int open(const char * pathname, int flags);
 int open(const char * pathname, int flags, mode_t mode);
 
 参数分析：
11 pathname --> 需要打开的文件的 路径+名字 （如果没有写路径则默认为当前路径）
12 flags --> 旗标（标志实质是就是一个数字）
13 O_RDONLY 以只读方式打开文件
14 O_WRONLY 以只写方式打开文件
15 O_RDWR 以可读写方式打开文件.
16 上述三种旗标是互斥的, 也就是不可同时使用,
17 但可与下列的旗标利用 OR(|)运算符组合.
18 O_CREAT 若欲打开的文件不存在则自动建立该文件.
19 O_EXCL 如果 O_CREAT 也被设置, 此指令会去检查文件是否存在. 文件若不存在则建立该文件, 否
20 O_NOCTTY 如果欲打开的文件为终端机设备时, 则不会将该终端机当成进程控制终端机.
21 O_TRUNC 若文件存在并且以可写的方式打开时, 此旗标会令文件长度清为 0, 而原来存于该文件的资
22 O_APPEND 当读写文件时会从文件尾开始移动, 也就是所写入的数据会以附加的方式加入到文件后面
23 O_NONBLOCK 以不可阻断的方式打开文件, 也就是无论有无数据读取或等待, 都会立即返回进程之中
24 O_NDELAY 同 O_NONBLOCK.
25 O_SYNC 以同步的方式打开文件.
26 O_NOFOLLOW 如果参数 pathname 所指的文件为一符号连接, 则会令打开文件失败.
27 O_DIRECTORY 如果参数 pathname 所指的文件并非为一目录, 则会令打开文件失败.
28 mode --> 文件的初始化权限（只有在创建新文件的时候生效）
29
30 返回值：
31 成功 返回一个新的文件描述符(new file descriptor)
32 int --> 整型 --> 编号 --> 该文件在本进程中的编号
33 失败 返回 -1 ， 错误号码会被设置

#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd1, fd2;

    // 使用第一个函数原型打开文件
    fd1 = open("file1.txt", O_RDONLY);
    if (fd1 == -1) {
        perror("open");
        exit(EXIT_FAILURE);
    }
    printf("File 1 opened successfully with file descriptor: %d\n", fd1);

    // 使用第二个函数原型打开文件，并设置权限为 0644
    fd2 = open("file2.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (fd2 == -1) {
        perror("open");
        exit(EXIT_FAILURE);
    }
    printf("File 2 opened successfully with file descriptor: %d\n", fd2);

    // 关闭文件描述符
    close(fd1);
    close(fd2);

    return 0;
}

```

写入文件 

write

```
1 头文件：
2 #include <unistd.h>
3
4 定义函数：
5 ssize_t write (int fd, const void * buf, size_t count);

7 参数分析：
8 fd --> 需要写入的文件的描述符
9 buf --> 需要写入的数据所在的内存地址
10 count --> 需要写入的字节数
11
12 返回值：
13 返回实际写入的字节数.
14 当有错误发生时则返回-1, 错误代码存入 errno 中

#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd;
    ssize_t bytes_written;
    const char *buffer = "Hello, World!\n";

    // 打开文件以写入方式
    fd = open("output.txt", O_WRONLY | O_CREAT | O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        exit(EXIT_FAILURE);
    }

    // 将数据写入文件
    bytes_written = write(fd, buffer, strlen(buffer));
    if (bytes_written == -1) {
        perror("write");
        exit(EXIT_FAILURE);
    }

    printf("Data successfully written to file.\n");

    // 关闭文件描述符
    close(fd);

    return 0;
}

```

读取文件

read

```
1 头文件：
2 #include <unistd.h>
3
4 定义函数：
5 ssize_t read(int fd, void * buf, size_t count);
6
7 参数分析：
8 fd --> 需要读取的文件的描述符
9 buf --> 读取到的数据存放的位置（用户的缓冲区）
10 count --> 需要读取的字节数
11
12 返回值：
13 成功 返回实际读到的字节数（如果返回值比count 小，则有可能到大文件的末尾）
14 错误 发生时则返回-1, 错误代码存入 errno 中

#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <unistd.h>

#define BUFFER_SIZE 1024

int main() {
    int fd;
    ssize_t bytes_read;
    char buffer[BUFFER_SIZE];

    // 打开文件以只读方式
    fd = open("input.txt", O_RDONLY);
    if (fd == -1) {
        perror("open");
        exit(EXIT_FAILURE);
    }

    // 从文件中读取数据
    bytes_read = read(fd, buffer, BUFFER_SIZE);
    if (bytes_read == -1) {
        perror("read");
        exit(EXIT_FAILURE);
    }

    // 打印读取的数据
    printf("Data read from file:\n%s\n", buffer);

    // 关闭文件描述符
    close(fd);

    return 0;
}

```

移动文件

lseek

```
1 头文件：
2 #include <sys/types.h>
3 #include <unistd.h>
4
5 定义函数 ：
6 off_t lseek(int fildes, off_t offset, int whence);
7
8 参数分析：
9 fildes --> 需要移动读写位置的文件的描述符
10 offset --> 偏移量设置
11 whence --> 偏移的模式
12 SEEK_SET 参数 offset 即为新的读写位置.
13 SEEK_CUR 以目前的读写位置往后增加 offset 个位移量.
14 SEEK_END 将读写位置指向文件尾后再增加 offset 个位移量.
15 当 whence 值为 SEEK_CUR 或SEEK_END 时, 参数 offet 允许负值的出现.
16
17 返回值：
18 成功时则返回目前的读写位置, 也就是距离文件开头多少个字节.
19 若有错误则返回-1, errno 会存放错误代码.
20

#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd;
    off_t new_offset;

    // 打开文件以读写方式
    fd = open("example.txt", O_RDWR);
    if (fd == -1) {
        perror("open");
        exit(EXIT_FAILURE);
    }

    // 将文件指针移动到文件末尾
    new_offset = lseek(fd, 0, SEEK_END);
    if (new_offset == -1) {
        perror("lseek");
        exit(EXIT_FAILURE);
    }
    printf("Current offset in file: %ld\n", new_offset);

    // 将文件指针移动回文件开头
    new_offset = lseek(fd, 0, SEEK_SET);
    if (new_offset == -1) {
        perror("lseek");
        exit(EXIT_FAILURE);
    }
    printf("Current offset in file: %ld\n", new_offset);

    // 关闭文件描述符
    close(fd);

    return 0;
}

```

## 4、C语言标准输入输出流

- 标准输入文件（stdin），通常对应终端的键盘；

- 标准输出文件（stdout）和标准错误输出文件（stderr），这两个文件都对应终端的屏幕。

- ```
  int main(){
  fprintf(stdout,"Hello ");
  fprintf(stderr,"World!");
  return0;
  }
  ```

```
World!Hello
```

- 原理：在默认情况下，stdout是行缓冲的，他的输出会放在一个buffer里面，只有到换行的时候，才会输出到屏幕。
- 而stderr是无缓冲的，会直接输出，举例来说就是printf(stdout, "xxxx") 和 printf(stdout, "xxxx\n")，前者会憋住，直到遇到新行才会一起输出。
- 而printf(stderr, "xxxxx")，不管有么有\n，都输出。

mmap函数

```
void *mmap(void *start, size_t length, int prot, int flags, int fd, off_t offsize);

start：指定映射区域的起始地址，通常设置为 NULL，让内核选择合适的地址。
length：指定映射区域的长度，以字节为单位。
prot：指定映射区域的保护模式，可以是以下几个值的组合：
PROT_READ：允许读取映射区域的内容。
PROT_WRITE：允许写入映射区域的内容。
PROT_EXEC：允许在映射区域执行代码。
PROT_NONE：不允许对映射区域进行任何操作。
flags：指定映射区域的选项，可以是以下几个值的组合：
MAP_SHARED：映射区域与文件共享修改。
MAP_PRIVATE：映射区域为进程私有，对映射区域的写入不会影响到原文件。
MAP_ANONYMOUS：创建一个匿名映射区域，不与文件关联。
fd：指定要映射的文件的文件描述符，如果不映射文件，则设置为 -1。
offset：指定文件中的偏移量，从该偏移量开始映射文件。对于不映射文件的情况，通常设置为 0。
下面是一个简单的示例代码，演示了如何使用 mmap 函数将一个文件映射到内存中：

#include <stdio.h>
#include <stdlib.h>
#include <sys/mman.h>
#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd;
    char *mapped;
    off_t file_size;

    // 打开文件以只读方式
    fd = open("example.txt", O_RDONLY);
    if (fd == -1) {
        perror("open");
        exit(EXIT_FAILURE);
    }

    // 获取文件大小
    file_size = lseek(fd, 0, SEEK_END);
    if (file_size == -1) {
        perror("lseek");
        exit(EXIT_FAILURE);
    }

    // 将文件映射到内存中
    mapped = mmap(NULL, file_size, PROT_READ, MAP_PRIVATE, fd, 0);
    if (mapped == MAP_FAILED) {
        perror("mmap");
        exit(EXIT_FAILURE);
    }

    // 打印文件内容
    printf("File content:\n%s\n", mapped);

    // 解除映射
    if (munmap(mapped, file_size) == -1) {
        perror("munmap");
        exit(EXIT_FAILURE);
    }

    // 关闭文件描述符
    close(fd);

    return 0;
}
```

文件控制

int ioctl(int d,int request, ...);

![image-20240419154743238](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240419154743238.png)

`ioctl` 函数是用于设备I/O控制的系统调用，在Linux中广泛使用。它允许用户空间程序与设备驱动程序进行通信，以执行各种控制操作。其基本语法如下：

```
int ioctl(int d, int request, ...);
```

其中：

- `d` 是一个打开的文件描述符，通常是指向设备文件的文件描述符。
- `request` 是一个整数值，用于指定要执行的操作类型，通常是一个特定设备驱动程序定义的常量。
- `...` 是可选参数，用于传递与请求相关的参数。这些参数的类型和数量取决于特定的 `request`。

`ioctl` 函数的返回值通常用于指示操作的成功或失败，成功时返回 0，失败时返回 -1，并设置全局变量 `errno` 以指示错误类型。

在使用 `ioctl` 函数时，常见的操作包括设置设备属性、读取设备状态、发送控制命令等。例如，一个常见的用法是通过 `ioctl` 函数设置串口的波特率、数据位、停止位和奇偶校验等参数。

```
#include <sys/ioctl.h> 

int ioctl(int fd, int cmd, ...) ;
```

## 5、标准IO缓冲区

标准 I/O 缓冲区是 C 语言中用于提高 I/O 操作性能的一种机制。在进行文件 I/O 操作时，标准库提供了三种类型的缓冲区：

1. **全缓冲（Fully Buffered）**：当数据量较大时，标准库会使用全缓冲，这意味着数据会先存储在内存中的缓冲区中，直到缓冲区填满或者调用了 `fflush()` 函数，然后再一次性写入文件。通常，当数据量超过一定大小（通常是几 KB）时，系统会自动选择全缓冲模式。
2. **行缓冲（Line Buffered）**：当使用行缓冲时，数据会存储在内存中的缓冲区中，直到遇到换行符 `\n` 或者调用了 `fflush()` 函数，然后才会写入文件。如果输出流连接到终端设备时（例如，`stdout` 默认是行缓冲的），通常会使用行缓冲。
3. **无缓冲（Unbuffered）**：数据不会在内存中缓冲，而是直接通过系统调用写入文件。通常，当输出流连接到终端设备时，标准错误流 `stderr` 是无缓冲的。

缓冲类型由 `setvbuf()` 函数设置，可以使用以下常量来指定缓冲类型：

- `_IOFBF`：全缓冲
- `_IOLBF`：行缓冲
- `_IONBF`：无缓冲

例如，要将标准输出流设置为全缓冲模式，可以使用以下代码：

```
c复制代码#include <stdio.h>

int main() {
    // 将标准输出流设置为全缓冲
    setvbuf(stdout, NULL, _IOFBF, BUFSIZ);

    // 此处进行输出操作...

    return 0;
}
```

缓冲区还可以通过 `setbuf()` 函数来设置，但它的使用方式相对简单，只能用来开启全缓冲或者无缓冲模式，并且不能关闭缓冲。



