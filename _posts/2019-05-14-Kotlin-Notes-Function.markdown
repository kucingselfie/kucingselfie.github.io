---
title: "Kotlin Notes: List, Map, Function"
tags: [kotlin, java]
comments: true
---

Hai, kali ini saya mau lanjutin artikel yang kemarin yaitu tentang bahasa pemrograman kotlin. Saya kasih judul 
kotlin notes agar artikel ini bisa menjadi catatan saya juga dikemudian hari. Karena terkadang saat kita belajar
sesuatu dan tidak mengulanginya kembali, kita bisa saja lupa, makanya cara saya biasanya dengan menulisnya kembali.
Di artikel ini kita akan belajar sedikit tentang List, Array, dan Function. Sebenarnya kedepannya masih banyak sekali, tapi kita
coba melangkah sedikit demi sedikit. Oh iya, disini saya tidak mengajarkan untuk "membuat sesuatu" atau tutorial-tutorialan.
Disini kita cuma akan belajar basic dari kotlin itu sendiri, tapi jangan khawatir, ini lebih penting dibanding langsung ngegas 
membuat sesuatu tanpa paham fundamentalnya. Oke tidak usah bacot

### List
List biasanya digunakan untuk menyimpan beberapa data, biasanya juga disebut Array. Cara membuat List di kotlin seperti ini

```
var nums = listOf(1,2,3,4)
```
untuk menyimpan variabel integer/angka.

Sedangkan untuk menampilkan listnya kita bisa lakukan seperti ini

```
for (i in nums) {
  println(i)
}
```
Hasil yang akan tampil adalah 1,2,3,4

Kita juga bisa print index dan datanya sekaligus dengan cara 

```
for ((i, e) in nums.withIndex()) {
  println("$i : $e")
}
```

Maka hasilnya : 

```
0 : 1
1 : 2
2 : 3
3 : 4
```

### Map
Map digunakan untuk menyimpan list data dengan key value pair. Jadi disetiap data ada key sebagai penanda untuk setiap valuenya.
Biasanya implementasinya digunakan untuk pengelompokan data. Syntax untuk membuat map di kotlin adalah : <br /> <br />
Pertama untuk put valuenya

```
var hm = HashMap<Int, String>()

hm["1"] = "Kucing"
hm["2"] = "Buaya"
```

Kemudian untuk menampilkan hasilnya

```
for ((key, name) in hm) {
  println("$key : $name")
}
```
"key" dan "name" bisa diganti ke apa saja yang kita suka.


### Function

Cara membuat function di kotlin

```
fun add(a : Int, b: Int) {

}
```

Sedangkan untuk function return type

```
fun add(a: Int, b: Int) : Int
{return a + b}
```

dimana ``Int`` di ujung kanan adalah tipe variabel returnnya

Syntax diatas juga bisa disingkat dengan menghilangkan curly brackets dan ``return`` nya

```
fun add(a: Int, b: Int) : Int = a + b
```

Jika ingin memberikan kondisi juga dapat ditulis seperti ini

```
fun add(a: Int, b: Int) : Int = if (a > b) a else b
```

### Default dan Named Parameters pada Function

Default parameter digunakan untuk memberikan default value pada parameter function. <br />
Contoh cara membuatnya di kotlin

```
fun calculate(amnt: Int, interest: Double = 0,04) : Int
{
  return (amnt + amnt * interest).toInt()
}
```

Cara panggilnya, kita cukup menambahkan satu parameter saja karena parameter kedua (interest) telah kita assign pada default paremeter

```
var totalAmount = calculate(50)
```

Untuk named parameters, kita bisa implementasikan pada saat memanggil function nya

```
var totalAmount = calculate(interest = 0,04, amnt = 50)
```

Jadi, walaupun parameternya kita tukar, nilainya tidak akan berubah karena setiap parameternya telah kita berikan named parameter.

Oke, itu dulu aja, Sekian, Thanks.
