---
title: Assembly - Mengambil Input dan Mencetaknya
author: kawaii-ghost
layout: post
---

# Mengambil Input dan mencetaknya

Sekarang kita akan belajar cara mengambil input kemudian mencetaknya menggunakan syscall linux.

Kita akan menggunakan syscall `read()` untuk membaca masukkan dari sebuah stream kemudian menyimpannya ke dalam memori.

Kurang lebih beginilah kodenya dalam bahasa C

```c
#include <unistd.h>

int main(void)
{
    static char str[5];
    ssize_t ret = read(STDIN_FILENO, str, sizeof(str));
    write(STDOUT_FILENO, str, ret);
    _exit(0);
}
```

Sekarang kita akan menulisnya dalam bentuk assembly.

Oh iya, kita akan menggunakan instruksi `xor` nantinya untuk mengisi nilai suatu register dengan nilai 0

Serta menggunakan bagian 32 bit register karena lebih efisien (tidak menggunakan REX prefix).

Parameter syscall `read()` kurang lebih sama seperti syscall `write()`.

| SYSCALL | RAX | RDI | RSI | RDX | R10 | R8 | R9 |
| ------- | --- | --- | --- | --- | --- | -- | -- |

| read   |  0  | fd  | addr | buf | --- | -- | -- |


```asm                           
[section .bss]
        text resb 5
        ; Alokasi memori statis sebanyak 5 bytes

[section .text]
        global _start
_start:
        xor eax, eax
        xor edi, edi ; STDIN_FILENO bernilai 0
        mov esi, text
        mov edx, 5
        syscall

        mov edi, 1
        mov esi, text
        mov rdx, rax ; return value dari write() dipakai sebagai count
        mov eax, 1
        syscall

        mov eax, 60
        xor edi, edi
        syscall
```

![image](https://user-images.githubusercontent.com/86765295/187007081-c7c105c7-f0a8-419a-b81f-8610345ad14e.png)

Di sini penulis mengetik "abcd" dan menekan tombol enter yang di mana juga akan memasukkan newline ke dalam input.
