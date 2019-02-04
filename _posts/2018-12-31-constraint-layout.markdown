---
title: "Android - Constraint Layout"
tags: [android]
---

Di dalam dunia android ada beberapa layout yang sering digunakan untuk desain xml diantaranya RelativeLayout, LinearLayout, FrameLayout, GridLayout, dan yang paling baru adalah ConstraintLayout. Sebelum mengetahui constraint layout bagaimana sih Android menampilkan View ? 
Di android sendiri ada step view rendering cycle :
- Measure pass
- Layout pass
- Rendering pass

Measure pass berjalan untuk mengukur seberapa besar ukuran view, biasanya digunakan width dan height. Layout pass berjalan ketika terjadi perubahan ukuran dan position (left, right, top dan bottom) pada layout. Setelah measure dan layout pass, maka draw() method akan berjalan untuk melakukan rendering view. Oke cukup.

Constraint Layout adalah Layout yang digunakan untuk membuat atau menggambar responsif  dan adaptif view di Android, constraint layout hadir untuk mengatasi masalah banyaknya device dengan ukuran layar yang berbeda-beda di Android. Constraint layout juga disebut sebagai unbundled support library sehingga dapat berjalan pada API level 9 (GIngerbread). 

Ketika kita menambahkan view atau contohnya TextView maka terdapat 4 titik diatas, bawah, kiri dan kanan untuk menambahkan atau menghapus constraint. Cara menambahkan constraint adalah dengan klik dan drag, dan cara menghapusnya dengan cara klik titik constraintnya. 

![Constraint](https://i.ibb.co/RPk9c2Z/Screen-Shot-2018-12-31-at-12-03-53-AM.png "Layout editor constraint layout")
Untuk ubah atribute dan lain-lain bisa langsung explore di layout editornya lol.

### Chains

Chains digunakan untuk managing space pada view. Fitur ini dapat menggantikan layout_weight pada LinearLayout. Fitur dari chains untuk saat ini dibagi jadi 3 :
- Spread packed
- Spread outside
- Spread inside

![Constraint](https://i.ibb.co/qRWRGvM/Screen-Shot-2018-12-31-at-12-14-23-AM.png "Spread packed")
Spread packed adalah default spread untuk memberikan space pada view, defaultnya adalah 8dp.

![Constraint](https://i.ibb.co/pzmybB0/Screen-Shot-2018-12-31-at-12-15-17-AM.png "Spread outside")
Spread outside untuk membagi space antara dua view outside dan inside dengan space yang sama.

![Constraint](https://i.ibb.co/B6nZjGr/Screen-Shot-2018-12-31-at-12-16-02-AM.png "Spread inside")
Spread inside untuk mengisi space hanya didalam viewnya atau alignParentStart dan alignParentEnd pada RelativeLayout.

### Virtual Helpers

Untuk saat ini virtual helpers ada 3  :
- Guidelines
- Barriers
- Groups

Guidelines digunakan untuk memberikan guideline vertical ataupun horizontal pada view dan setiap view dapat dibuat constraint ke guideline. Contohnya seperti gambar dibawah :

![Constraint](https://i.ibb.co/wJ5SNM1/Screen-Shot-2018-12-31-at-12-21-47-AM.png "Constrain guidelines")

Barriers digunakan untuk membuat barrier/pembatas antara view yang satu dan view yang lain. Contoh kasusnya adalah view dengan fitur internalization atau adanya perbedaan bahasa, dengan barrier kita dapat membuat view responsif dan menyesuaikan perubahan layout. Contohnya seperti gambar dibawah :

![Constraint](https://i.ibb.co/qsVwtY2/Screen-Shot-2018-12-31-at-12-36-07-AM.png "Constraint barriers")
Layout dalam bahasa inggris.

![Constraint](https://i.ibb.co/Jp2mVDQ/Screen-Shot-2018-12-31-at-12-41-14-AM.png "Constraint barriers")
Layout dalam bahasa jerman lol.

![Constraint](https://i.ibb.co/P97HtGH/Screen-Shot-2018-12-31-at-12-42-59-AM.png "Constraint barriers")
Setelah pake barrier. Langsung aman wkwkw.

Terakhir adalah Groups yaitu fitur untuk mengelompokkan view menjadi viewgroup tetapi tidak menggunakan nested layout seperti pada linearLayout.

Untuk saat ini masih itu sih fitur dari constraint layout yang dikembangkan Google tetapi tentu sangat berguna untuk membuat responsif layout juga dapat mengurangi double taxation atau nested viewgroup di Android. Silahkan dicoba ya. Sekian thanks.









