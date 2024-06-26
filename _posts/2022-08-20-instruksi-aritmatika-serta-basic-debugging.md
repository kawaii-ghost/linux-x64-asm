---
title: Assembly - Instruksi Aritmatika serta Basic Debugging
author: kawaii-ghost
layout: post
---

# Instruksi Aritmatika serta basic debugging

Di bab ini kita akan belajar membuat Instuksi Aritmatika, dan juga men-debug-nya menggunakan GDB.

## Aritmatika dalam komputer
Jadi aritmatika dalam assembly adalah sebuah instruksi yang di eksekusi melalui [**Arithmetic Logical Unit (ALU)**](https://en.wikipedia.org/wiki/Arithmetic_logic_unit) pada CPU. Seperti matematika pada umumnya, aritmatika di assembly memiliki fungsi yang sama, yaitu pertambahan, pengurangan, perkalian, dan seterusnya.

### Tipe Aritmatika
Berikut merupakan jenis-jenis aritmatika yang bisa kita gunakan di assembly.
| Tipe Aritmatika | Assembly | Notation |
| --------------- | -------- |----------|
| `Addition`      | **add**  | **+**    |
| `Subtraction`   | **sub**  | **-**    |
| `Multiplication`| **mul/imul**  | **x**    |
| `Division`      | **div/idiv**  | **/**    |
| `Increment`     | **inc**  | **+1**   |
| `Decrement`     | **dec**  | **-1**   |

Default ukuran semua operasi di atas adalah 32 bit.

> Dikarenakan x86 ini bertipe CISC, saya menyederhanakan cara penggunaannya ke yang paling mudah

### Addition/Substraction
Addition di assembly mengambil 2 input dan menggabungkannya menjadi satu. Sedangkan substraction mengurangi input pertama dengan input kedua. Bedanya disini kita memiliki source dan destination, dimana source ini akan ditambahkan pada destination address yang dituju.

```asm
add <dest>, <src>
sub <dest>, <src>
```
contoh:
```asm
xor eax, eax    ; eax = 0
add eax, 100    ; eax += 100
sub eax, 10     ; eax -= 10
```
Instruksi diatas akan mengubah __eax__ menjadi 90.

### Multiplication

Ada dua macam instruksi, `mul` untuk unsigned dan `imul` untuk signed.

```asm
mul/imul <src>
```
Untuk ukuran operan:

- 8 bit, mengalikan sumber dengan `al` menghasilkan 8 bit `al`.
- 16 bit, mengalikan `ax`, menghasilkan 32 bit `dx:ax`, 16 bit atas dan 16 bit bawah.
- 32 bit, mengalikan `eax`, menghasilkan 64 bit, `edx:eax`, 32 bit atas dan 32 bit bawah.
- 64 bit, mengalikan `rax`, menghasilkan 128 bit, `rdx:rax`, 64 bit atas dan 64 bit bawah.

| Ukuran | Dest | Hasil |
| ----- | ---- | ---- |
| 8 bit | `ah` | `al` |
| 16 bit | `ax` | `dx:ax` |
| 32 bit | `eax` | `edx:eax` |
| 64 bit | `rax` | `rdx:rax` |


contoh:

```asm
mov eax, 2      ; eax = 2
mov edi, 4      ; edi = 4
imul edi        ; edx:eax = eax * edi
```
Maka nilai __eax__ akan menjadi 8, dan nilai __edx__ menjadi 0.

### Division

Seperti perkalian, `idiv` untuk signed dan `div` untuk unsigned.

```asm
div/idiv <src>
```

Untuk ukuran sumber:

- 8 bit, `ax` dibagikan dengan sumber, menyimpan hasilnya di `al`, sisa pembagian di `ah`.
- 16 bit, `dx:ax` 32 bit dibagikan , menyimpan hasil di `ax`, sisanya  di `dx`.
- 32 bit, `edx:eax` 64 bit dibagikan, menyimpan hasil di `eax`, sisanya di `edx`.
- 64 bit, `rdx:rax` 128 bit dibagi, menyimpan hasil di `rax`, sisanya di `rdx`.

<br/>

| Ukuran | Numerator | Hasil | Sisa |
----------------|-----------|-------|------|
|     8 bit     |     `ax`    |  `al`   |  `ah`  |
| 16 bit | `dx:ax` | `ax` | `dx` |
| 32 bit | `edx:eax` | `eax` | `edx` |
| 64 bit | `rdx:rax` | `rax` | `rdx` |

<br/>

Tidak seperti `imul/mul`, `idiv/div` membutuhkan instruksi khusus.


Untuk `div`, jika kita hanya ingin membagi dengan nilai 16 bit, 32 bit, atau 64 bit.

nilai `ax`, `edx`, atau `rdx` harus 0. kecuali jika ingin nilai yang dibagi sebesar 2 kali lipat ukuran tersbut.

Serta `idiv`, nilai `ax`, `eax`, atau `rax` harus dibikin sign-extend ke dalam gabungan 2 register.
- `cwd` (convert word to dword) untuk `ax`, `dx:ax` = (signed)`ax`
- `cdq` (convert dword to qword) untuk `eax`, `edx:eax` = (signed)`eax`
- `cqo` (convert qword to oword) untuk `rax`, `rdx:rax` = (signed)`rax`

contoh:

```asm
mov eax, 100    ; eax = 100
cdq             ; edx:eax = (signed)eax
mov edi, 10     ; edi = 10
idiv edi        ; edx = edx:eax % edi
                ; eax = edx:eax / edi
```
Nilai `eax` adalah 10, dan `edx` adalah 0.


```asm
xor edx, edx    ; edx = 0
mov eax, 4      ; eax = 4
mov edi, 10     ; edi = 10
div edi         ; edx = edx:eax % edi
                ; eax = edx:eax / edi  
```
Maka nilai `eax` akan menjadi 0, dan nilai `edx` menjadi 4.

### Increment/Decrement
Increment dalam assembly berfungsi untuk menambah nilai dari destinasi sebanyak 1. Sedangkan decrement mengurangi nilai dari destinasi sebanyak 1.

```asm
inc <dest>
dec <dest>
```

contoh:

```asm
xor eax, eax  ; eax = 0
inc eax       ; eax += 1
inc eax       ; eax += 1
```
Maka hasilnya adalah 2.
```asm
mov eax, 10 ; eax = 10
dec eax     ; eax -= 1
dec eax     ; eax -= 1
```
maka hasilnya adalah 8.

> Perhatian! instruksi inc dan dec tidak disarankan untuk digunakan karena bisa menyebabkan partial register flags stall.
> Serta dalam CPU Modern, add dan sub lebih cepat dibandingkan inc dan dec

## Debugging di GDB
### Basic Command
| Perintah        | Fungsi   | Penggunaan |
| --------------- | -------- | ---------- |
| `help`          | **Menampilkan opsi pada suatu perintah**| **help \<argument(optional)>** |
| `run`           | **Menjalankan program** | **run <argument(optional)>** |
| `disass`        | **Mendisassamble** | **disass \<symbol atau pointer>**|
| `set`           | **Setting variable konfigurasi pada gdb**| **set \<argument>  \<value>** |
| `break`         | **Set breakpoint**| **break \<symbol atau \*address>** |
| `next`          | **Lanjut ke instruksi selanjutnya**| **next** |
| `continue`      | **Melanjutkan program setelah breakpoint** | **continue**|
| `info`          | **Menampilkan informasi**| **info \<argument>** |
| `commands`       | **Membuat auto script saat menginjak breakpoint** | **commands <range of breakpoint, example: 1-4, 2>** |

Example:
### Men-disassamble dan menganalisis instruksi yang sedang dieksekusi
Kita akan menggunakan instruksi dibawah, sebagai uji coba.
```asm
section .text
        global _start
_start:
        xor eax, eax ; eax = 0
        add eax, 10 ; eax += 10
        add eax, 20 ; eax += 20
        add eax, 30 ; eax += 30

exit:
        mov eax, 60 ; exit syscall
        xor edi, edi; edi = 0
        syscall
```
Setelah kita meng-compile instruksi diatas, kita akan menganalisa bagaimana fungsi add bekerja menggunakan GDB.

- Pertama kita akan menjalankan gdb, dengan menggunakan perintah `gdb <nama_program>`
- Setelah masuk kedalam Command Line Interface di GDB, kita akan mengetikkan perintah `set disassembly-flavor intel` untuk mengubah format disassembly-nya menjadi format intel, agar lebih mudah dibaca.
- Lalu kita akan set `step-mode` variable menjadi on, agar kita dapat mendebug instruksi assembly satu persatu, `set step-mode on`.

Ok, setelah men-setup GDB-nya, kita langsung saja ke bagian utama, yaitu menganalisa instruksi `add` pada program.
- Pertama kita akan set breakpoint pada symbol `_start`, `break _start`
- Next, kita jalankan programnya, `run`.
- Ketik `next` untuk masuk ke instruksi berikutnya.
- Kita akan meliahat registernya, dan ingat disini value rax masih 0, `info registers`.
output:


![](https://user-images.githubusercontent.com/92920739/186277915-e939fba4-92ca-49f0-b1ca-372ede0746c3.png)


- Kita `next` lagi.
- Lalu lihat registernya, `info register`. Maka valuenya akan berubah, seperti dibawah


![](https://user-images.githubusercontent.com/92920739/186277755-63b0db85-0f79-4581-8c8d-4c9ca8f73558.png)


Kenapa bisa berubah? yup, karena kita baru saja melewati instruksi `add eax, 10`

- Ketik `disasss _start`

output:


![](https://user-images.githubusercontent.com/92920739/186277505-fd15700d-f256-48df-a44e-cf1fb245df32.png)


Bisa dilihat diatas, saat kita men-disassemble symbol `_start`, anak panah menuju ke instruki ke-3, yang artinya instruksi ke-2 telah selesai di eksekusi, dan alhasil menambahkan 0xa atau 10 ke rax.

### Membuat script automasi yang berjalan saat menginjak breakpoint.
```asm
section .text
        global _start
_start:
        mov eax, 2
        mov ebx, 4
        mul ebx   
        mov ebx, 4
        mul ebx   

exit:
        mov eax, 60 
        xor edi, edi
        syscall
```
- Pertama kita set dulu breakpointnya, contoh:

![image](https://user-images.githubusercontent.com/92920739/186538139-e332986c-70bc-4ac4-9e12-755a2e906311.png)
> Disini saya menyetel breakpoint pada symbol `_start` instruksi pertama, kedua, dan ketiga. Untuk mendapatkan address dari suatu instruksi, kalian bisa menggunakan `disass`.

- Lalu ketikkan perintah `commands`, dan tentukan breakpoint mana saja yang mau kita buat skrip automasi.
> Note: Untuk melihat list breakpoint kalian bisa menggunakan perintah `info breakpoints`
> ![image](https://user-images.githubusercontent.com/92920739/186538403-9353a00d-96a9-4d48-8871-76300ebaa466.png)

![image](https://user-images.githubusercontent.com/92920739/186535285-20c39ffa-f380-44a9-bc98-5ac67bf9b91a.png)

- Setelah itu kalian bisa mengetikkan skrip yang nantinya akan dieksekusi saat breakpoint.
contoh:

![image](https://user-images.githubusercontent.com/92920739/186537141-17fd49c6-c5d0-4fb1-9a1a-3d23df21bdb5.png)

- Saat kita `run`, dan menginjak breakpoint maka ama muncul output dari script yang kita buat tadi.

![asd](https://user-images.githubusercontent.com/92920739/186574728-5b509fd7-78f2-4d6b-83a5-e4e1f55312b9.png)

![image](https://user-images.githubusercontent.com/92920739/186539009-18dc61a1-974c-4c42-9332-e2c5858dbf0d.png)

![image](https://user-images.githubusercontent.com/92920739/186539084-ac2297c9-515b-4438-8bfd-c3f8bc6ec5f0.png)
