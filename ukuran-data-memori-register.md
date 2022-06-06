# Register dan Memori

Di dalam bab ini, kita akan mengenal tentang ukuran-ukuran data di dalam komputer

## Ukuran Data dan Model Data

_Apa itu Data Model ?_

Model Data adalah representasi sebuah ukuran data dalam tipe data scalar di C/C++.

Contohnya, Linux menggunakan Model data **lp64** sementara Windows menggunakan Model data **llp64**.

Karena Linux menggunakan Data Model **lp64** berarti ukuran `long` serta `pointer` adalah 64 bit.

Ada sedikitnya 4 ukuran data yang harus diketahui.

| Nama | Ukuran |
|------|--------|
| **byte** | 1 byte / 8 bit|
| **word** | 2 byte / 16 bit|
| double word / **dword** | 4 byte / 32 bit|
| quad word / **qword** | 8 byte / 64 bit|

Representasi model data tersebut dapat dilihat dalam tabel di bawah ini

| Tipe data | Ukuran |
| --------- | ------ |
| `char` | **byte** |
| `short` | **word** |
| `int` | **dword** |
| `long` | **qword** |
| `long long` | **qword** |

Jadi, tidak ada jaminan bahwa Ukuran suatu tipe data lebih kecil daripada tipe data lainnya dan sebaliknya.

Karena semua OS bisa memakai model data yang berbeda.

<br/>

> _Jadi, bagaimana agar dapat menggunakan tipe data yang portable ?_

Kalian bisa menggunakan `typedef` `(u)int_leastX_t` di mana X adalah ukuran bitnya

Sebagai, contoh `(u)int_least64_t`.

Dengan begitu, kita tidak perlu khawatir akan ukuran tipe data yang berbeda karena `typedef` 

secara otomatis akan menyesuaikan dengan model data yang digunakan.



## Static Memori




