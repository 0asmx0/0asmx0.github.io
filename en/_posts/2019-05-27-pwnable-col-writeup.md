---
layout: post
title: Pwnable.kr - col | Writeup
lang: en
lang-ref: pwnable-col-writeup
description: 
---

# Analiz

Program argüman olarak verdiğimiz değeri bir hash fonksiyonuna sokuyor. Çıkan değer eğer hashcode değişkenine eşitse başarılı oluyoruz.

Hash fonksiyonunu inceleyelim:

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


Program argümanı string olarak alıyor ve bu değere bir integer pointer atıyor. Sonrasında da integer pointerların point ettiği değerleri toplayıp hash'i döndürüyor. Yani olan şey aslında şu,

Programa **AAAABBBBCCCCDDDDEEEE** argümanını verdiğimizi düşünelim. Bu argümanlar stack'e şu şekilde yerleşecek.

```c
0x41414141 0x42424242 0x43434343 0x44444444 0x45454545
```

**check_password** fonksiyonuna verilen **p** argümanı char pointer tipinde. **p**'nin değerlerini şu şekilde gösterebiliriz:

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

C programlama dilinde int'in boyutu 4 byte'tır. Program **p** değişkenini **int \*** şeklinde ip değerine atınca bu sefer yukarıdaki gibi elemanlar 1 byte'lık kısımlar değil , integer'ın boyutu 4 byte olduğundan ötürü 4 byte'lık kısımlar tutacak.
Yani **ip** değişkeni şu şekilde olacak:

```c
ip[0] -> 0x41414141 // AAAA
ip[1] -> 0x42424242 // BBBB
ip[2] -> 0x43434343 // CCCC
ip[3] -> 0x44444444 // DDDD
ip[4] -> 0x45454545 // EEEE
```

Biz bu değerlerin toplamının **0x21DD09EC** yani **568134124** olduğu bir argüman girip sonuca ulaşabiliriz.
Bunu yapmanın en kolay yolu string bir değer girmek yerine hexadecimal bir değer vererek direktman memory'ye etki etmek.

# Çözüm
```sh
./col `python -c "print '\xc8\xce\xc5\x06' * 4 + '\xcc\xce\xc5\x06'"`
```
