---
title: Assembly - Ukuran Data Memori Statis Register
author: kawaii-ghost
layout: post
---

# Register dan Memori

Di dalam bab ini, kita akan mengenal tentang ukuran-ukuran data di dalam komputer, serta memori statis dan register.

## Ukuran Data dan Model Data

> _Apa itu Model Data?_

Model Data adalah representasi sebuah ukuran data ke dalam tipe data tunggal di C/C++.

Contohnya, Linux menggunakan Model data **lp64** sementara Windows menggunakan Model data **llp64**.

Karena Linux menggunakan Model data **lp64** berarti ukuran `long` serta `pointer` adalah 64 bit.

Ada sedikitnya 4 ukuran data yang harus diketahui.

| Nama | Ukuran |
|------|--------|
| **byte** | 1 byte / 8 bit|
| **word** | 2 byte / 16 bit|
| **dword** / _double word_ | 4 byte / 32 bit|
| **qword** / _quad word_ | 8 byte / 64 bit|

Representasi model data tersebut dapat dilihat dalam tabel di bawah ini

| Tipe data | Ukuran |
| --------- | ------ |
| `char` | **byte** |
| `short` | **word** |
| `int` | **dword** |
| `long` | **qword** |
| `long long` | **qword** |

Alasan kenapa `long` di Windows ukurannya 32 bit, sementara di Linux ukurannya 64 bit

karena Windows menggunakan Model data **llp64** yang mengharuskan menggunakan tipe data **long long** untuk

menggunakan memori sebesar 64 bit.

Jadi, tidak ada jaminan bahwa ukuran suatu tipe data lebih kecil / lebih besar dari tipe data lainnya dan sebaliknya.

Karena semua OS bisa saja memakai model data yang berbeda.

<br/>

> _Jadi, bagaimana agar dapat menggunakan tipe data yang portable ?_

Kalian bisa menggunakan typedef `(u)int_leastX_t` dari `cstdint` di mana X adalah ukuran bitnya

Sebagai, contoh `(u)int_least64_t`.

Dengan begitu, kita tidak perlu khawatir akan ukuran tipe data yang berbeda karena `typedef` tersebut

secara otomatis akan menyesuaikan dengan model data yang digunakan.

<br/>

> _Apakah, ada ukuran data lainnya seelain keempat ukuran di atas ?_

Yup, ada **oword** , **tbyte**, **yword** , **zword**.

| Nama | Ukuran |
| --- | ---|
| **oword** / _octoword_| 16 byte / 128 bit |
| **tbyte** / _ten bytes_ | 10 byte |
| **yword** / _YMM word_ | 32 byte / 256 bit |
| **zword** / _ZMM word_ | 64 byte / 512 bit |

## Static Memori
Memori statis dialokasikan di awal program, dan hanya akan dibebaskan _ketika program berakhir_.
### Mendefinisikan data statis

Ini cukup mudah, kita hanya perlu menulis `dx` di mana `x` awal huruf ukuran data yang kita ingin deklarasikan 

contohnya `db` untuk _define byte(s)_ dan `dq` untuk _define qword(s)_.

`data db 1,2,3` sama seperti `static char data[] = {1, 2, 3};`

`data dq 1,2,3` sama seperti `static long data[] = {1, 2, 3};`

Jika kalian masih tidak paham, perhatikan tabel dibawah ini

| _define xxx_ | **dx** |
| ----- | ----- |
| _define byte(s)_ | `db` |
| _define word(s)_ | `dw` |
| _define dword(s)_ | `dd` |
| _define qword(s)_ | `dq` |
| _define oword(s)_ | `do` |
| _define tbyte(s)_ | `dt` |
| _define yword(s)_ | `dy` |
| _define zword(s)_ | `dz` |

### Deklarasi data statis

Sama seperti mendefinisikan data statis, bedanya kalau definisi menggunakan huruf `d` , sementara deklarasi menggunakan kata `res`.

`dx` menjadi `resx`.

`data resb 10` sama seperti `static char data[10];`

`data resq 10` sama seperti `static long data[10];`


| _reserve xxx_ | **resx** |
| ----- | ----- |
| _reserve byte(s)_ | `resb` |
| _reserve word(s)_ | `resw` |
| _reserve dword(s)_ | `resd` |
| _reserve qword(s)_ | `resq` |
| _reserve oword(s)_ | `reso` |
| _reserve tbyte(s)_ | `rest` |
| _reserve yword(s)_ | `resy` |
| _reserve zword(s)_ | `resz` |

<br/>

Lengkapnya, kalian bisa baca doc nasm di sini https://www.nasm.us/doc/nasmdoc3.html#section-3.2

## Register


### Pengenalan Register

Ada dua jenis register, **GPR** atau _General Purpose Register_  dan _Flag Register_. 

_Flag Register_ akan kita bahas di bab prosedur, fungsi, percabangan.

Ada 16 GPR dalam arch x86-64.
- **RAX** | Accumulator Register
- **RCX** | Counter Register
- **RDX** | Data Register
- **RBX** | Base Register
- **RSP** | Stack Pointer Register
- **RBP** | Base Pointer Register
- **RSI** | Source Index Register
- **RDI** | Destination Index Register
- **R8** sampai **R15**

> _Register hanyalah nama_
> 
> Kalian bebas memakai register untuk operasi apapun asalkan
> 
> Memperhatikan ABI (tidak sembarangan memakai preserved register).

15 GPR tadi dibagi lagi menjadi 2, Scratch Register dan Preserved Register.

Scratch register adalah register yang bebas kita pakai.

Preserved register adalah register yang tidak bisa sembarangan dipakai.

**rax**, **rdi**, **rsi**, **rdx**, **rcx**, **r8**, **r9**, **r10**, dan **r11** adalah scratch registers.

**rbx**, **rsp**, **rbp**, **r12**, **r13**, **r14**, dan **r15** adalah preserved register.

Register-register di atas berukuran 64 bit, dan memiliki bagian kecilnya, seperti bagian 32 bit, 16 bit, dan 8 bit.

![gambar](https://user-images.githubusercontent.com/86765295/172949892-966f9a4c-f692-45f3-8523-60f38d1e569d.png)

| **64 bit** | 32 bit | 16 bit | 8 bit |
| ------ | ------ | ----- | ----- |
| **`RAX`** | `EAX` | `AX` | `AH` / `AL`|
| **`RCX`** | `ECX` | `CX` | `CH` / `CL`|
| **`RDX`** | `EDX` | `DX` | `DH` / `DL` |
| **`RBX`** | `EBX` | `BX` | `BH` / `BL` |
| _**`RSP`**_ | _`ESP`_ | _`SP`_ | _**`SPL`**_ |
| _**`RBP`**_ | _`EBP`_ | _`BP`_ | _**`BPL`**_ |
| **`RSI`** | `ESI` | `SI` | **`SIL`** |
| **`RDI`** | `EDI` | `DI` | **`DIL`** |
| **`R8`** | **`R8D`** | **`R8W`** | **`R8B`** |
| - | - |- | -|
| **`R15`** | **`R15D`** | **`R15W`** | **`R15B`** |

<br/>

> _Apa beda dari 2 bagian register 8 bit yang terdapat pada `rax`, `rcx`, `rdx`, dan `rbx` ?_

**ax**, **cx**, **dx**, dan **bx** berukuran 16 bit yang terdiri dari 8 bit atas dan 8 bit bawah.

**h** menandakan higher bits, atau bagian 8 bit atas dari register 16 bit.

**l** menandakan lower bits, atau bagian 8 bit bawah dari register 16 bit.



Contoh,  ukuran 8 bit.

Jika 127 direpresentasikan dalam bentuk biner.

`0111 1111`

8 bit ini dipecah menjadi 4 bit atas dan 4 bit bawah

`0111` dan `1111`

 - `0111` ini menjadi 4 bit atas

 - `1111` ini menjadi 4 bit bawah

<br/>

Ada hal yang perlu diperhatikan saat mengubah-ubah nilai suatu register.

- Mengubah register 8 bit hanya akan mengubah register tersebut.
- Mengubah register 16 bit akan mengubah nilai register 16 bit dan 8 bit.
- Mengubah register 32 bit selain mengubah register 32 bit dan di bawahnya, juga mengubah bagian 32 bit atas register 64 bit menjadi 0.
- Mengubah register 64 bit mengubah semua register yang ada di bawahnya.

Untuk semua register yang berasal dari ekstensi 64 bit (semua tulisan yang tebal di dalam tabel) **membutuhkan encoding tambahan**. (REX prefix).

REX.W prefix untuk `rax` hingga `rdi`

REX.R prefix untuk `r8` hingga `r15`

Dan juga, saat menggunakan REX prefix kita tidak bisa mengakses `ah`, `ch`, `dh` , dan `bh`.

Sebagai gantinya kita bisa mengakses`spl`, `bpl`, `sil`, dan `dil`.

Contoh, `mov eax, 1` lebih optimal dibandingkan `mov rax, 1` jika ingin mengubah nilai `rax`.

<br/>

![image](https://user-images.githubusercontent.com/86765295/186908334-d54c4edd-080e-4eb6-9de9-c20f72895bc3.png)


Jika kita lihat gambar di atas, opcode `mov eax, 1` lebih sedikit dibandingkan `mov rax, 1` karena mengakses `rax` menggunakan REX.R prefix.

`mov eax, 1` lebih optimal karena tidak menggunakan REX.R prefix.

Begitu juga yang lainnya. Selain `R8` hingga `R15`. Disarankan untuk mengubah bagian 32 bit saja karena secara implicit juga mengubah bagian 64 bit (32 bit atas).

### Sign Extend dan Zero Extend

Perlu diingat, komputer memakai basis 2 (biner) dan manusia memakai basis 10.

127 jika diubah ke bentuk biner 8 bit `0111 1111`.

Tapi bilangan biner 255 serta -1 dalam ukuran 8 bit memiliki bit yang sama! `1111 1111`.

Jika kita tidak mengetahui apakah itu signed atau unsigned kita tidak bisa memperkirakan

apakah nilai bit `1111 1111` adalah -1 atau 255.

Hal ini dapat menyebabkan masalah ketika kita ingin memindahkan nilai antar register yang berbeda ukuran.

Karena -1 dalam ukuran 8 bit berbeda dengan -1 dalam ukuran 16 bit

Dalam Arsitektur x86-64 ini, kita bisa menentukan sebuah bilangan `signed` atau `unsigned`

ketika memindahkan nilai antar register yang berbeda ukuran menggunakan `movsx` dan `movzx`.

`movsx` untuk sign extend atau signed.

`movzx` untuk zero extend atau unsigned.

Contoh

```asm
mov al, 255
movzx cx, dl
```

Dengan begitu, nilai `cx` akan sama dengan `al` yaitu 255. meskipun nilai bit mereka berbeda.

Ref : 
- https://en.wikipedia.org/wiki/Signed_number_representations
- https://en.wikipedia.org/wiki/Two%27s_complement
