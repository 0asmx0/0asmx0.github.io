---
layout: post
title: Pwnable.kr - fd | Writeup
lang: en
lang-ref: pwnable-fd-writeup
description:
---

### Need to know
- Linux file descriptors

### Analyse

When we inspect **fd.c**, we see that it reads a value into **buf** variable by using the **read** function.

```c
readlen = read(fd, buf, 32);
```

Then program continues like this:

```c
if(!strcmp("LETMEWIN\n", buf)){
 printf("good job :)\n");
 system("/bin/cat flag");
 exit(0);
}
```

**read** function basically gets an input from the given **file descriptor**. Since standard input has already a file descriptor which is **STDIN**, give this as **fd** to the **read** function and make it get input from standard input.

```c
int fd = atoi( argv[1] ) - 0x1234;
```

Program converts the first argument to integer and it substracts 0x1234 from it (which is 4660 in base 10). **STDIN** equals to 0, we should give 4660 to the program.

### Çözüm
```
./fd 4660
[ Program waits for input ] LETMEWIN  
```
