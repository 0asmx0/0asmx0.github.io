---
layout: post
title: Pwnable.kr - flag | Writeup
lang: en
lang-ref: pwnable-flag-writeup
description: 
---

### İpucu
- UPX packer

### Analiz

**flag** dosyasını gdb'de açmaya çalıştığımızda ve **disas main** dediğimizde bir sorunla karşılaşıyoruz.

```
(gdb) disas main
No symbol table is loaded.  Use the "file" command.
```

Daha sonrasında **strings** komutunu kullandığımızda sorunun ne olduğunu anlayabiliriz.

```
strings ./flag

UPX!
...
$Info: This file is packed with the UPX executable packer http://upx.sf.net $
...
```

Meğersem program decompile edilmesin diye **UPX** ile işleme sokulmuş. Bunun üstesinden gelmek için **upx** komutunu kullanıyoruz.

```sh
upx -d flag
```

Şimdi ise programı çalıştırıyoruz ve program bize **flag**'i **strcpy** kullanarak bir variable'ın içine kopyalayacağını söylüyor. Hemen **gdb** ile bu **flag**'i bulalım.

```
(gdb) disas main
Dump of assembler code for function main:
   ...
   0x0000000000401184 <+32>:	mov    rdx,QWORD PTR [rip+0x2c0ee5]        # 0x6c2070 <flag>
   ...   
End of assembler dump.
(gdb) x/wx 0x6c2070
0x6c2070 <flag>:	0x00496628
(gdb) x/s 0x00496628
0x496628:	**SANSÜRLENMİŞ FLAG**
```

Görüldüğü üzere önce **flag**'e point eden değeri bulduk. Daha sonra da bu değeri **x/s** yaparak direkt okuyabildik.
