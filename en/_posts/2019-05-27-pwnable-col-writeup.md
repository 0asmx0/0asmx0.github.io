---
layout: post
title: Pwnable.kr - col | Writeup
lang: en
lang-ref: pwnable-col-writeup
description:
---

# Analyse

Program argüman olarak verdiğimiz değeri bir hash fonksiyonuna sokuyor. Çıkan değer eğer hashcode değişkenine eşitse başarılı oluyoruz.

Program calculates a hash from the argument we give then compares it with the variable hashcode. If they are same, we get the flag.

Let's inspect hash function:

```c
unsigned long check_password(const char* p){
 int* ip = (int*)p;
 int i;
 int res=0;
 for(i=0; i<5; i++){
  res += ip[i];
 }
 return res;
}
```

Let's give **AAAABBBBCCCCDDDDEEEE** to the program as argument. This argument will be placed to the stack like this

```c
0x41414141 0x42424242 0x43434343 0x44444444 0x45454545
```

Type of the parameter **p** which is given to the **check_password** is char pointer. We can show it like this:

```c
p[0] = 'A';
p[1] = 'A';
p[2] = 'A';
p[3] = 'A';
p[4] = 'B';
p[5] = 'B';
...
...
```

In C programming, size of int is 4 bytes. So when we assign an integer pointer to **p**, each element will point to 4 bytes of data instead of 1.
So it will be like this:

```c
ip[0] -> 0x41414141 // AAAA
ip[1] -> 0x42424242 // BBBB
ip[2] -> 0x43434343 // CCCC
ip[3] -> 0x44444444 // DDDD
ip[4] -> 0x45454545 // EEEE
```

We need to find 5 integers and their sums should be **0x21DD09EC**. To do this, we use python to write hexadecimals to the memory.

# Solution
```sh
./col `python -c "print '\xc8\xce\xc5\x06' * 4 + '\xcc\xce\xc5\x06'"`
```
