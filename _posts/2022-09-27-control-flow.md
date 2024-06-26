---
title: Assembly - Control Flow
author: kawaii-ghost
layout: post
---

# Alur Program

Kita akan belajar bagaimana alur program bekerja seperti percabangan, perulangan, serta fungsi bekerja.

## Percabangan
Ada sebuah register khusus `RIP` yang berfungsi sebagai _Instruction Pointer_.

Register tersebut memuat nilai alamat instruksi yang akan dijalankan nantinya.

CPU berpatokan pada `RIP` untuk mengetahui alamat yang akan dieksekusinya.

```asm
main:
        xor edi, edi    ; rip
        mov esi, 15    ; rip + 1
        call kill       ; rip + 2
```

Ada dua jenis percabangan, _unconditional jump_ dan _conditional jump_.


### Unconditional jump

Pertama, kita akan mengenal instruksi `jmp`.

Kalian tahu `goto`?. Yap, Instruksi `jmp` bekerja seperti keyword `goto` dalam bahasa C/C++.

Contohnya

```c
_start:
        goto end
end:
        exit(0);
```

Di dalam bahasa assembly

```asm
_start:
        jmp end
end:
        xor edi, edi
        call exit
```

Karena `jmp` tidak membutuhkan informasi apapun tentang suatu kondisi maka `jmp` juga digunakan untuk seluruh percabangan yang tidak  melakukan perbandingan atau selalu benar,

`jmp label` akan memuat alamat `label` ke dalam `rip` sehingga CPU akan mengeksekusi instruksi di alamat *label* yang ditunjuk oleh `rip`. 

Contohnya `if (1)`. Dalam hal tersebut, Compiler langsung menghasilkan `jmp` (atau tidak sama sekali) dikarenakan tidak ada perbandingan yang perlu dilakukan.

### Conditional jump

Conditional jump berjalan apabila suatu kondisi terpenuhi. Dalam kasus ini, conditional jump akan melakukan melihat informasi kondisi di _Register Flag_. 
Register Flag adalah register yang menyimpan informasi hasil dari komparasi.

Ada 3 Register Flags yang biasanya dipakai, _Sign Flag_, _Zero Flag_, dan _Carry Flag_.


- _Sign Flag_ (**SF**) adalah flag yang berisi status perlakuan kedua operan sebagai `signed` atau `unsigned`. 
- _Zero Flag_ (**ZF**) adalah flag yang berisi status hasil komparasi.
- _Carry Flag_ (**CF**) adalah flag yang berfungsi untuk memberi tahu jika hasil komparasi terjadi overflow pada operasi unsigned.

Informasi-informasi di dalam `rflags` dibuat setelah melakukan komparasi melalui instruksi `cmp` atau `test`.

Contoh kasusnya adalah operator logika `==`.

Jika nilai kedua operan sama, maka _Zero Flag_ akan bernilai 1.

Control-Flow dengan nilai **ZF** sama dengan 1 akan dieksekusi dengan instruksi `JE` (Jump if Equal).

Jika tidak maka _Zero Flag_ akan bernilai 0.

Control-Flow dengan nilai **ZF** sama dengan 0 akan dieksekusi dengan instruksi `JNE` (Jump if Not Equal).

```asm
section .rdata
        szBerhasil db `RBX == 0\n\0`
        nLenBer equ $-szBerhasil
        szGagal db `RBX != 0\n\0`
        nLenGal equ $-szGagal
section .text
        global _start
_start:
        mov eax, 1
        mov edi, 1

        push rbx ; save rbx ke stack, nanti di chapter stack penjelasan lanjutnya
        xor ebx, ebx
        cmp rbx, 0 ; Kalian juga bisa menggunakan test rbx, rbx untuk mengecek nilai rbx apakah 0

        je true
        jne false
true:
        mov esi, szBerhasil
        mov edx, nLenBer
        syscall
        jmp exit
false:
        mov esi, szGagal
        mov edx, nLenGal
        syscall
        jmp exit
exit:
        pop rbx ; restore rbx from stack
        mov eax, 60
        xor edi, edi
        syscall
```

![gambar](https://github.com/kawaii-ghost/linux-x64-asm/assets/86765295/1bcf9d7c-b116-41d4-9fa5-90f97cabb113)


#### Tugas
Nah, sekarang tugas kalian bereksperimen dengan flag dan instruksi lainnya di [sini](https://learn.microsoft.com/en-us/windows-hardware/drivers/debugger/x86-architecture#conditions).


## Perulangan
Dengan memodifikasi percabangan, kita bisa membuat perulangan :).

Contoh kasus

```c
static char szText[] = "I like you!\n";
for (int i = 0; i < 143; i++)
        write(STDOUT_FILENO, szText, sizeof(szText) - 1);
```

Itu akan berubah menjadi

```c
static char szText[] = "I like you!\n";
int i = 0; 
while (i < 9) {
        write(STDOUT_FILENO, szText, sizeof(szText) - 1);
        i++;
}
```

Kemudian berubah lagi menjadi

```c
static char szText[] = "I like you!\n";
int i = 0;
loop:
        if (i < 9) {
                write(STDOUT_FILENO, szText, sizeof(szText) - 1);
                i++;
                goto loop;
        }
```

 > Note : Disarankan untuk memakai non-volatile register agar nilai di dalam register terjamin tidak berubah setelah pemanggilan fungsi

```asm
_start:
        push rbx
        xor ebx, ebx
loop:
        mov eax, 1
        mov edi, 1
        mov esi, szText
        mov edx, 35
        syscall

        add ebx, 1
        cmp ebx, 9
        jl loop

        pop rbx
```
Kurang lebih begitulah hasil konversi dari C ke Assembly, https://godbolt.org/z/odYKvd3h1
