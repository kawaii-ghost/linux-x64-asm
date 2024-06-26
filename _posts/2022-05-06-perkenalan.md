---
title: Assembly - Perkenalan
author: kawaii-ghost
layout: post
---

# Hello, world!

## Persiapan
Halo, Selamat datang. Sebelum mulai, mari kita persiapkan alat-alat yang akan digunakan.

### Alat-alat yang dibutuhkan:
* [**NASM** \(Netwide Assembler\)](https://nasm.us)
* [**ld** \(GNU linker\)](https://www.gnu.org/software/binutils/) atau [**lld** \(LLVM ELF linker\)](https://lld.llvm.org/)
* **x86-64** (GNU/)**Linux**
* [**GDB** \(GNU Debugger\)](https://sourceware.org/gdb/)

## Membuat program dasar
Buatlah sebuah file dengan nama `hello.asm` menggunakan text editor kesukaan kalian

Di sini saya menggunakan text editor `nano`. jalankan

```bash
$ nano hello.asm
```

Tulislah kode dibawah ini

```asm
[section .rodata]
        msg db `Hello, world!\n\0`

[section .text]
        global _start
        
_start:
        mov rax, 1
        mov rdi, 1
        mov rsi, msg
        mov rdx, 14
        syscall
        
        mov rax, 60
        mov rdi, 0
        syscall
```
![gambar](https://user-images.githubusercontent.com/86765295/180227237-52e68366-c459-411b-a2ac-19f64c90fff2.png)


Setelah selesai, tekan `Ctrl+X`, `Y`, dan `enter` .

Jalankan 

```bash
$ nasm -f elf64 hello.asm
```

Hasi keluaran dari `nasm` belum bisa dijalankan karena hanya berupa object file saja.

Oleh karena itu jalankan 

```bash
$ ld hello.o -o hello
```

, agar linker menghasilkan executable.

Kita sudah berhasil membuat executablenya, sekarang mari kita eksekusi.

```
$ ./hello
```

Tada, program yang kita buat menghasilkan output _"Hello, world!"_

# Dasar-dasar

## Bagian-bagian program.
Dalam sebuah program, terdapat beberapa bagian/seksi.

Setidaknya ada 4 bagian yang umum

- **.data**
- **.rodata**
- **.bss**
- **.text**

> *Perlu dingat, isi semua bagian di atas (kecuali .text) adalah `static`*

### \[section .data\]

Lokasi data-data / variabel yang sudah diisi / diinisialisasi berada di sini. 

`section .data` bersifat *read write*, bisa dibaca dan bisa ditulis.

### \[section .rodata\]

Tempat data konstan atau tidak bisa diubah.

Dalam bahasa C/C++, tempat konstanta (`const`) global atau  konstanta statis (`const static`) berada.

Semua literal string juga berada di sana, ex `const char *str = "www\n"`.

Seperti namanya, `section .rodata` ini *read only*, hanya bisa dibaca.

### \[section .bss\]

Alamat memori statis yang dialokasi berada di sini.

> Note: unitialized static data secara default akan bernilai 0

`section .bss` bersifat *read write*.

### \[section .text\]

Kode-kode yang akan dieksekusi di program berada di sini.

Sifatnya *read exec*.

<br/>

> Apakah moderasi tiap bagian bisa diubah ?

Ya bisa, tapi untuk sekarang kita tidak akan membahas itu.

## System Call

> _Apa itu system call ?_

System call adalah cara program berinteraksi dengan sistem operasi melalui kernel.

Saat kita melakukan syscall, kita menyuruh kernel untuk melakukan suatu fungsi kernel.

_Gak Paham ?_

Bahasa awamnya, **system call adalah fungsi-fungsi sistem yang tersedia di kernel.**

## Instruksi dasar
NASM menggunakan syntax intel.

Bentuk sintaksnya kurang lebih sebagai berikut

```
instruksi dest, src
```
<br/>

`mov` (_move_) adalah instruksi untuk memindahkan data ke dalam memori atau register.

`mov x, y` sama seperti `x = y`.

<br/>

`syscall` adalah instruksi untuk memanggil system call dari kernel

## Calling convention
Perhatikan penggunaan register untuk system call Linux x64 ini.

- **rax** digunakan untuk menentukan nomor syscall
- **rdi** digunakan untuk memberikan argumen pertama
- **rsi** digunakan untuk memberikan argumen kedua
- **rdx** digunakan untuk memberikan argumen ketiga
- **r10** digunakan untuk memberikan argumen keempat
- **r8** digunakan untuk memberikan argumen kelima
- **r9** digunakan untuk memberikan argumen keenam


| SYSCALL_NR | arg0 | arg 1 | arg2 | arg3 | arg4 | arg5 |
|------------|------|--------|--------|-----|----|----|
| RAX | RDI | RSI | RDX | R10 | R8 | R9 |

Kalian juga bisa baca-baca `man 2 syscall`.

Penjelasan lebih lanjut tentang calling-convention ini akan kita lanjut di bab calling convention.

<br/>

> Fun fact: Calling convention di atas adalah kernelspace System V ABI untuk arsitektur amd64
> 
> Yang di mana juga digunakan untuk semua os \*nix seperti BSD, Darwin, yang berarsitektur amd64

# Penjelasan 
Untuk membuat bagian, kita menggunakan keyword `section .nama`  

di mana `.nama` adalah nama bagian yang kita ingin buat.

##  \[section .rodata\]

`db` merupakan singkatan dari "_**d**efine **b**yte(s)_"

Seperti yang kita tahu, ukuran `char` di Linux x64 adalah 1 byte / 8 bit.

Berarti `msg` merupakan array of `char` / `byte`.

`'\n'` escape character untuk _newline_

`'\0'` untuk karakter **NULL**.

Jadi 

```asm
msg db `Hello, world!\n\0`
```

sama seperti

```c
static const char msg[] = "Hello, world!\n";
```

Dalam bahasa C.

> Note : Dikarenakan **msg** berada dalam **.rodata**, maka **msg** bersifat statis (`static`) dan konstan (`const`) .

## \[section .text\]

Sebelum kita lanjut ke pembahasan selanjutnya, saya ingin membuat analogi system call kernel dengan menu restoran.

**nomenu** digunakan untuk menentukan menu makanan (seperti **rax** yang menentukan nomor syscall).

**ket1** digunakan untuk keterangan pertama / argumen pertama (seperti **rdi**).

Berikut adalah contoh tabel menu makanan

| Nama makanan | nomenu | ket1 |
| ------------ | ------- | ---- |
| Lemper Jepang     |    1    | jumlah | 
| Tahu Bulat | 2 | jumlah |

<br/>

Instruksi `isi` digunakan untuk mengisi.

Instruksi `pesan` digunakan untuk memanggil pelayan atau menandakan pesanan kita sudah ditulis.

<br/>

Kita diberikan kertas pesanan sebagai berikut

| No menu | ket1 |
| ------- | ----- |
|  ...  |   ...  |

Jika kita ingin memesan Lemper jepang dengan jumlah 5 buah maka kita mengisi tabelnya sebagai berikut

| No menu | ket1 |
| ------- | ---- |
|    1     |  5 |

Jika dituliskan dalam bentuk pseudocode assembly kurang lebih seperti di bawah ini.

```
isi nomenu, 1
; Mengisi nomenu dengan angka 1
isi ket1, 5
; Mengisi ket1 dengan angka 5
pesan
; Memesan lemper jepang sebanyak 5 buah
```
Maka pelayan akan menerima pesanan "Lemper Jepang", berdasarkan **nomenu** dan menyajikan 5 buah lemper berdasarkan **ket1**.

Dalam fungsi C/C++, kode di atas sama seperti

`lemper_jepang(5);`

----------------------------------------------------------------------------

Oke, kita kembali ke materi.

`global _start` digunakan agar linker bisa mengakses label **_start** dari luar.

label **_start** sama seperti fungsi **main** dalam bahasa C/C++.

### WRITE

Kita akan melakukan syscall **write** untuk mengeluarkan output _"Hello, world!"_ ke command line.

Prototipe **sys_write** adalah sebagai berikut

```c
write(int fd, const void *buf, size_t count);
```
|  syscall  | rax | rdi | rsi |  rdx  | r10 | r8 | r9 |
| --------- | --- | --- | --- | ---- | ---- | --- | -- |
| write |  1  | fd  | buf | count |  -  |  - |  - |

> Referensi : man 2 write

<br/>

```c
mov rax, 1
``` 
`rax = 1`

Memasukkan nilai 1 ke dalam **rax**.

System call mana yang akan dipanggil ditentukan oleh register ini.

System Call `write` memiliki nomor 1 atau System Call nomor 1 adalah `write`.

Sekarang, register **rax** sudah terisi dengan nilai 1

<br/>

###
```asm
mov rdi, 1
```

`rdi = 1` 

Memasukkan nomor 1 ke dalam **rdi**.

Argumen pertama untuk **sys_write** adalah nomor file descriptor untuk ditulis

Karena kita ingin menampilkan pesan ke **stdout** maka kita mengisi **rdi** dengan nilai 1.

<br/>

Saat suatu program dijalankan, setidaknya ada 3 stream yang secara default sudah terbuka.
 
`stdin` untuk menerima input, 

`stdout` menampilkan output,

dan `stderr` untuk menampilkan pesan diagnosa atau error.
 
Ketiga stream tadi memilki nomor file descriptor yang tetap.

Berikut adalah tabel 3 standard streams dan nomornya.

   | fd | value |
   | ---| ------ |
   | stdin | 0 |
   | stdout | 1 |
   | stderr | 2|
   
 `<unistd.h>` memilki tiga macro yang mendefinisikan nomor ketiga standard stream.
 
 ```c
 #define STDIN_FILENO 0
 #define STDOUT_FILENO 1
 #define STDERR_FILENO 2
 ```
 - https://en.wikipedia.org/wiki/File_descriptor

<br/>

###
```asm
mov rsi, msg
``` 

`rsi = msg` Mengisi **rsi** dengan nilai **msg**.

Argumen kedua syscall **write** adalah alamat data yang ingin ditulis.

Jadi sekarang register **rsi** berisi nilai alamat dari string **msg**

<br/>

###
```asm
mov rdx, 14
```
`rdx = 14` 

Mengisi **rdx** dengan nilai 14.

Argumen ketiga **sys_write** adalah jumlah bytes yang ingin ditulis

Ukuran **msg** tanpa karakter **NULL** adalah 14 bytes.

<br/>

Kode-kode di atas tadi jika diubah ke dalam bahasa C sama seperti

```c
write(rdi, rsi, rdx)
write(1, msg, 14)
```

**sys_write** sudah selesai sekarang kita bisa memanggil kernel dengan instruksi `syscall`

### EXIT

Selanjutnya kita akan melakukan syscall exit untuk menghentikan program

Prototipenya

```c
_exit(int status)
```

| syscall  | rax |   rdi  | rsi | rdx | r10 | r8 | r9 |
| ---------|-----|--------|-----|-----|-----|----|----|
| sys_exit |  60 | status |  -  |  -  |  -  |  - |  - |

> Referensi : man 2 exit
<br/>

```asm
mov rax, 60
```
`rax = 60` 

Mengisi **rax** dengan nilai 60.

Nomor syscall untuk **sys_exit** adalah 60.

<br/>

###
```asm
mov rdi, 0
```
`rdi = 0` 

Mengisi register **rdi**  dengan nilai 0.

<br/>

Hasilnya

```c
_exit(0)
```

syscall **\_exit** sudah selesai jadi kita memanggil kernel dengan instruksi `syscall` untuk melakukan **sys\_exit**.

<br/>

Pelajaran pertama untuk x64 Linux Assembly sudah selesai >//<, selanjutnya kita akan membahas register dan memori.


## Extra

Kalian bisa mengecek syscall yang dilakukan suatu program menggunakan `strace`.

![gambar](https://user-images.githubusercontent.com/86765295/170260303-672ce31a-5961-4f71-a30f-12dadea00f97.png)

Nampak di atas kalau program yang tadi kita buat melakukan **sys\_write** dan **sys\_exit** .

<br/>

Kalian juga bisa memanggil **syscall-syscall** di atas menggunakan bahasa C.

```c
#include <unistd.h>

int main(void)
{
        static const char msg[] = "Hello, world!\n";
        write(STDOUT_FILENO, msg, 14);
        _exit(0);
}
```

Simpan file dengan nama `main.s` .
Compile dengan perintah di bawah ini.

`$ cc main.c -O2 -S -masm=intel -fno-pie`

Compiler akan memberikan output file assembly dari program yang akan dicompile.

Outputnya akan menghasilkan `main.s`.

Berikut hasil assembly dari clang.

![gambar](https://user-images.githubusercontent.com/86765295/170271193-076765ca-a6eb-4b34-9b4b-0613b9a23b13.png)

_Lho kok beda dengan kode diatas, mengapa eax, edi, ... bukan rax, rdi, ... serta xor edi, edi bukan mov rdi, 0 ?_

Compiler mengoptimisasi instruksi assemblynya, kita akan membahas alasannya seiring bertambahnya materi yang kita pelajari.

Untuk tabel syscall linux x64 bisa dilihat di [sini](https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md#x86_64-64_bit)

Untuk yang offline bisa dilihat di `man syscalls` dan nomornya 

bagi yang menggunakan glibc ada di sini `/usr/include/x86_64-linux-gnu/asm/unistd_64.h`
