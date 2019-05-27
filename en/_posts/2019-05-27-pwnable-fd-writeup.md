---
layout: post
title: Pwnable.kr - fd | Writeup
lang: en
lang-ref: pwnable-fd-writeup
description: 
---

### Bilinmesi gerekenler
- Linux file descriptors

### Analiz

**fd.c** dosyasını incelediğimizde **read** fonksiyonuyla **buf** değişkeninin içine bir değer okunduğunu görüyoruz.

```c
readlen = read(fd, buf, 32);
```

Sonrasında da program şöyle devam ediyor.

```c
if(!strcmp("LETMEWIN\n", buf)){
 printf("good job :)\n");
 system("/bin/cat flag");
 exit(0);
}
```


**buf**'ın içine istediğimiz değeri koyabilmek için **read** fonksiyonunun kullandığı **file descriptor**'u **STDIN**  yapmamız. Bu sayede program bizden girdi bekleyecek ve **buf**'ın değerini direktman değiştirebileceğiz.

```c
int fd = atoi( argv[1] ) - 0x1234;
```

Program yukarıda verdiğimiz 1. argumanı integer'a çevirip sonrasında 16 lık tabanda 0x1234 değerinden çıkarıyor ve bunu fd'ye eşitliyor. **STDIN**'in numerik karşılığı 0, yani girdiğimiz değer 0x1234'ün 10 luk tabanda karşılığı olmalı. Terminale python komutunu girip, sonrasında da 0x1234 yazarak sayının 10 tabanında değerini alabiliriz.

### Çözüm
```sh
./fd 4660
```
