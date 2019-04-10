---
title: "Belajar Kotlin : Seru Nggak Sih ?"
tags: [kotlin, java]
comments: true
---


Hai, karena beberapa hari ini saya sedang belajar kotlin jadi kali ini saya mau sharing sedikit dasar-dasar kotlin, 
bagaimana syntax dasarnya, bagus tidak sih atau malah nyusahin. Jadi kita mulai aja.


### Var dan Val

Var dan Val adalah keyword ketika kita ingin mendefinisikan suatu variabel di kotlin dimana Var adalah variabel yang valuenya
bisa di re-assign sedangkan Val adalah variabel yang valuenya tidak bisa di re-assign atau diubah. Kalo di javascript val itu 
seperti ``const`` dan keyword ``final`` di Java.
Cara penulisan variabel di kotlin seperti ini :

```
var angka: Int = 2

val angka: Int = 3
```

atau untuk string

```
var nama: String = "Alif"

val nama: String = "Firdaus"
```
Jadi penulisannya nama variabel terlebih dahulu kemudian semicolon diikuti tipe variabelnya.

### Membuat Objek di Kotlin

Cara membuat objek di kotlin agak beda dengan Java. Jika di Java seperti ini:

``` 
Kucing kucing = new Kucing();
```

Maka di Kotlin kita cukup menghilangkan ``new`` keywordnya sehingga akan jadi seperti ini

``` 
var kucing = Kucing()
```

kita juga tidak butuh semicolonnya jadi lumayan oke sih

### String Template

yaitu menambahkan sebuah expression ke dalam string. Kalo yang ini sangat-sangat mirip dengan javascript. Penulisannya

```
val umur: Int = 5
var umurKucing: String = "Umur Kucing $umur tahun"

println(umurKucing)
```
Outputnya: Umur Kucing 5 tahun

Bagaimana jika kita mau mengambil value dari sebuah objek ? Maka kita bisa menulisnya seperti ini, kita coba ambil dari objek
kucing yang tadi

```
var umurKucing: String = "Umur Kucing ${kucing.umur} tahun"
```

Outputnya: Umur Kucing 5 tahun

<b>"Do We need getter and setter in Kotlin ?"</b>

<b>"No, We don't need"</b>

### If Else
Cara membuat kondisi di kotlin sama dengan di Java, hanya saja di kotlin kita dapat mengembalikannya sebagai expression

Contoh:

```
var angka1: Int = 20
var angka2: Int = 23

if(angka1 < angka2)
println("Benar")
else
println("Salah")
```

Untuk yang memiliki ``return`` value kita bisa menulisnya seperti ini dengan menghilangkan keyword ``return`` nya

```
if(angka1 < angka2) angka1 else angka2
```

Kita juga bisa menulis seperti ini untuk mengembalikannya sebagai expression

```
var angkanya: String = if(angka1 < angka2) println("Benar") else println("Salah")
```

### When

When adalah expression ``Switch`` pada kotlin. Cara penulisannya

```
var angka1: Int = 12

when(angka1) {
  10 -> println("Salah")
  11 -> println("Salah")
  12 -> println("Benar")
}
```
Kita tidak perlu kata ``case:`` atau ``break;`` layaknya di Java. Keren juga ya

### For Loop dan Range

<b>Range</b> digunakan untuk membuat range nilai tertentu di kotlin contoh

```
var nilai: Int = 1..5
```

Cara print outputnya yaitu dengan menggunakan <b>For Loop</b> . Cara penulisan <b>For Loop</b> di kotlin adalah

```
for(i in nilai) {
  println(i)
}
```

Outputnya: 1 2 3 4 5

Bagaimana jika kita mau buat range kebalikannya dari 5 ke 1 ?
Apakah possible seperti ini

```
var nilai: Int = 5..1
```

ternyata tidak bisa hehe

Tapi kita bisa achieve dengan menambahkan operator bernama ``downTo``

Jadinya seperti ini

```
var nilai: Int = 5 downTo 1
```
Maka outputnya adalah: 5 4 3 2 1

<b>ATAUUU</b>

Bisa juga dengan melakukan reversed pada for loopnya dengan cara seperti ini

```
for(i in nilai.reversed()) {
  println(i)
}
```

Maka outputnya sama: 5 4 3 2 1

<b>TUNGGU SEBENTARR</b>

<b>"Apakah kita bisa menerapkan range pada karakter?"</b>

Jawabannya adalah bisa dengan cara seperti ini

```
var karakter: String = 'A'..'E'

for(c in karakter){
  println(c)
}
```

Maka outputnya : A B C D E

Oke kayaknya sekian dulu, sebenarnya ada banyak sekali hal yang menarik di pemrograman kotlin ini, tapi saya akan lanjutkan 
pada artikel selanjutnya. Berikan komentarmu dibawah jika ada yang salah pada artikel ini karena sesungguhnya saya juga masih
belajar. Thanks :)


{% include disqus.html %}
