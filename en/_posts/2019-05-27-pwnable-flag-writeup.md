---
layout: post
title: Pwnable.kr - flag | Writeup
lang: en
lang-ref: pwnable-flag-writeup
description:
---

### Hint
- UPX packer

### Analyse

When we open **flag** with gdb and run **disas main**, an error message shows up.

```
(gdb) disas main
No symbol table is loaded.  Use the "file" command.
```

Then when we use **strings** command, we can immediately understand the problem.

```
strings ./flag

UPX!
...
$Info: This file is packed with the UPX executable packer http://upx.sf.net $
...
```

Program is packed with **UPX** packer. For more information about this process, please google it. To unpack the program, we use **upx** command:

```sh
upx -d flag
```

Then when we execute the program, it says that it will **strcpy** the flag to a variable. Let's find the **flag** with **gdb**.

### Solution

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
0x496628:	**CENSORED**
```
