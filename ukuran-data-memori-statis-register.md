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

Jadi, tidak ada jaminan bahwa ukuran suatu tipe data lebih kecil / lebih besa dari tipe data lainnya dan sebaliknya.

Karena semua OS bisa saja memakai model data yang berbeda.

<br/>

> _Jadi, bagaimana agar dapat menggunakan tipe data yang portable ?_

Kalian bisa menggunakan `typedef` `(u)int_leastX_t` di mana X adalah ukuran bitnya

Sebagai, contoh `(u)int_least64_t`.

Dengan begitu, kita tidak perlu khawatir akan ukuran tipe data yang berbeda karena `typedef` 

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

Ada dua jenis register, **GPR** atau _General Purpose Register_  dan _Flag Register_. 

_Flag Register_ akan kita bahas di bab prosedur, fungsi, percabangan.

Ada 15 GPR dalam arch x86-64.

- **RAX** | Accumulator Register
- **RCX** | Counter Register
- **RDX** | Data Register
- **RBX** | Base Register
- **RSP** | Stack Pointer Register
- **RBP** | Base Poiner Register
- **RSI** | Source Index Register
- **RDI** | Destination Index Register
- **R8** sampai **R15**

> _Register hanyalah nama_
> 
> Kalian bebas memakai register untuk operasi apapun asalkan
> 
> Mengikuti ABI (tidak sembarangan memakai preserved register).

15 GPR tadi dibagi lagi menjadi 2, Scratch Register dan Preserved Register.

Scratch register adalah register yang bebas kita pakai.

**rax**, **rdi**, **rsi**, **rdx**, **rcx**, **r8**, **r9**, **r10**, dan **r11**

adalah scratch registers.

Preserved register adalah register yang tidak bisa sembarangan dipakai.

**rbx**, **rsp**, **rbp**, **r12**, **r13**, **r14**, dan **r15**

adalah preserved register.











