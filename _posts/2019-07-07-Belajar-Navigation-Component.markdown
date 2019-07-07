---
title: "Android : Menggunakan Navigation Component"
tags: [kotlin, android, jetpack]
comments: true
---

Hi, kali ini saya akan mencoba sharing sedikit tentang Navigation Component di Android. 
Apa itu Navigation Component ? Navigation Component adalah sebuah library yang termasuk bagian dari Android Jetpack yang dibuat untuk 
mempermudah developer untuk membuat navigation antar Activity atau Fragment. Apa itu Android Jetpack ? Android Jetpack adalah library 
atau komponen yang dibuat oleh Google untuk mempermudah developing android app dengan memberikan komponen, rule dan arsitekturnya sendiri yang kesemuanya digabung dan tersimpan dalam library bernama “androidX”. 
Android Jetpack sendiri ada beberapa tetapi kali ini yang akan kita pelajari adalah Navigation Component. 

Sebelumnya di dalam navigation sendiri ada 3 prinsip :
1.	There’s always starting place yaitu Screen pertama yang dilihat user ketika pertama kali membuka sebuah app dan menjadi screen terakhir ketika user akan menutup sebuah app.
2.	You can always Go back yaitu Screen yang kita buat harus memiliki fungsi back dari current destination ke start destinationnya.
3.	Up goes back yaitu Up button harus memiliki fungsi back yang sama dengan system back button. Up button adalah tombol yang biasa kita lihat di pojok kiri atas sebuah app sedangkan system back button adalah tombol yang terdapat di pojok kanan bawah sebuah app atau handphone.

Oke kita langsung ngoding aja ya.

Pertama buat project aja dulu di Android Studionya. Konfigurasi projectnya seperti ini, saya centang “use androidx” dan memakai Bahasa Kotlin. 

kemudian di project build.gradle buat config untuk versionnya :

![Navigation](https://i.ibb.co/PFf0dLh/Screen-Shot-2019-07-06-at-10-02-53.png "Project Setup")

```
ext {
    version_kotlin = "1.3.31"
    version_core = "1.0.2"
    version_constraint_layout = "1.1.3"
    version_lifecycle_extensions = "2.0.0"
    version_material = "1.0.0"
    version_navigation = "1.0.0"
}
```

Setelah itu ke app build.gradle import navigation component sekalian sama material design:

```
// Material Design
implementation "com.google.android.material:material:$version_material"
implementation 'com.android.support:support-v4:28.0.0'

implementation "android.arch.navigation:navigation-fragment-ktx:$version_navigation"
implementation "android.arch.navigation:navigation-ui-ktx:$version_navigation"
```

Dan enable data binding dengan menambahkan kode ini juga ke build,gradle :

```
dataBinding {
    enabled = true
}
```

Setelah itu kita coba koneksikan dulu layout dan activitynya dengan data binding. Pertama kita buka activity_main.xml. 
Ubah layoutnya ke LinearLayout kemudian bungkus di dalam tag <layout></layout> seperti ini :

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">
    </LinearLayout>
</layout>
```

Setelah itu di MainActivty kita tambahkan kode berikut :

```
val binding = DataBindingUtil.setContentView<ActivityMainBinding>(this, R.layout.activity_main)
```

Dengan ini kita sudah berhasil melakukan binding layout ke Activitynya.

Sekarang kita buat Fragment pertama untuk di masukkan ke Activitynya. Yang belum tahu Fragment tolong dibaca dulu [ini](https://developer.android.com/guide/components/fragments?hl=ID). 

Untuk membuat sebuah fragment kita klik kanan pada directory com.example/blabla – New – Fragment – Fragment (Blank). 
Beri nama terserah anda asal jangan nama mantan kemudian unchecked pilihan include fragment factory method dan include interface callbacks. 
Disini saya beri nama HelloFragment. Klik Finish.

![Navigation](https://i.ibb.co/z8VyrjJ/Screen-Shot-2019-07-06-at-11-24-22.png "Membuat Fragment")

Masuk ke fragment_hello.xml, kita coba desain layoutnya seperti ini :

```
<?xml version="1.0" encoding="utf-8"?>
<layout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto" xmlns:tools="http://schemas.android.com/tools"
        tools:context=".HelloFragment">
    <androidx.constraintlayout.widget.ConstraintLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent">
        <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:textSize="20sp"
                android:text="@string/hello_fragment" android:id="@+id/textView"
                app:layout_constraintBottom_toTopOf="@+id/btn_hello_fragment" android:layout_marginTop="8dp"
                app:layout_constraintTop_toTopOf="parent" android:layout_marginEnd="8dp"
                app:layout_constraintEnd_toEndOf="parent" android:layout_marginStart="8dp"
                app:layout_constraintStart_toStartOf="parent"/>
        <Button
                android:id="@+id/btn_hello_fragment"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="@android:color/holo_orange_light"
                android:text="@string/navigate" android:layout_marginBottom="16dp"
                app:layout_constraintBottom_toBottomOf="parent" app:layout_constraintEnd_toEndOf="parent"
                android:layout_marginEnd="16dp" app:layout_constraintStart_toStartOf="parent"
                android:layout_marginStart="16dp"/>

    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```

![Navigation](https://i.ibb.co/b57JV9R/Screen-Shot-2019-07-06-at-11-49-17.png "Layout Hello Fragment")

Kemudian aktifin data binding pada fragment, taruh di dalam onCreateView ya:

```
// Inflate the layout for this fragment
val binding: FragmentHelloBinding = DataBindingUtil.inflate(inflater, R.layout.fragment_hello, container, false)
return binding.root
```

Oke sekarang kita mau buat Navigation Graph. Navigation Graph sendiri adalah fitur terbaru dari Android Studio yang memungkinkan kita untuk membuat fungsi navigation tetapi dalam tampilan UI gitu. 
Untuk membuatnya kita ke res folder klik kanan – New – Android Resource File – Ubah Resource type dan name menjadi navigation kemudian klik OK.

Kembali ke layout activity_main.xml tambahkan Host Fragment dan koneksikan fragment ke Navigation graph sehingga kodenya akan seperti ini :

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android" xmlns:app="http://schemas.android.com/apk/res-auto">
    <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">
        <fragment
                android:id="@+id/navHostFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:name="androidx.navigation.fragment.NavHostFragment"
                app:defaultNavHost="true"
                app:navGraph="@navigation/navigation"
        />
    </LinearLayout>
</layout>
```

Kemudian kita ke navigation.xml tambahkan HelloFragment ke navigation graph dengan klik New Destination – HelloFragment :

![Navigation](https://i.ibb.co/zFJS2MG/Screen-Shot-2019-07-07-at-11-32-43.png "Hello Fragment")

Buat satu fragment lagi sebagai screen kedua atau screen destination kita.  Ikuti step diatas untuk membuat fragment, saya beri nama HelloJugaFragment. 
Untuk layoutnya samain aja tapi hilangin tombolnya :

```
<?xml version="1.0" encoding="utf-8"?>
<layout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto">
  <androidx.constraintlayout.widget.ConstraintLayout
          android:layout_width="match_parent"
          android:layout_height="match_parent">
    <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:textSize="20sp"
            android:text="Hello Juga :)"
            android:id="@+id/textView"
            android:layout_marginTop="8dp"
            app:layout_constraintTop_toTopOf="parent" android:layout_marginEnd="8dp"
            app:layout_constraintEnd_toEndOf="parent" android:layout_marginStart="8dp"
            app:layout_constraintStart_toStartOf="parent" android:layout_marginBottom="8dp"
            app:layout_constraintBottom_toBottomOf="parent"/>
  </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```

Tambahkan HelloJugaFragment ke navigation graph Klik New Destination – HelloJugaFragment. 
Sehingga screen kita sudah ada 2 seperti ini :

![Navigation](https://i.ibb.co/cLCRqYH/Screen-Shot-2019-07-07-at-11-41-51.png "Hello Fragment")

Sekarang untuk nge-handle navigationnya kita buat connection dari HelloFragment ke HelloJugaFragment maka fungsi tersebut akan membuat sebuah “action”.

![Navigation](https://i.ibb.co/99k7L2c/Screen-Shot-2019-07-07-at-11-45-13.png "Hello Fragment")

Nah setelah itu untuk membuat navigation ketika tombolnya di klik kita ke HelloFragment kemudian tambahkan kode berikut :

```
binding.btnHelloFragment.setOnClickListener { view ->
    view.findNavController().navigate(R.id.action_helloFragment_to_helloJugaFragment)
}
```

Kode diatas akan mengeksekusi “action” dari navigation graph sehingga sampai disini kita sudah bisa berpindah dari screen HelloFragment ke HelloJugaFragment.

Sekarang untuk menambahkan Up navigation, kita ke MainActivity, kemudian tambahkan kode berikut untuk setup Up navigationnya. Kode di MainActivity :

```
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val binding = DataBindingUtil.setContentView<ActivityMainBinding>(this, R.layout.activity_main)
        val navController = this.findNavController(R.id.navHostFragment)
        NavigationUI.setupActionBarWithNavController(this, navController)
    }

    override fun onSupportNavigateUp(): Boolean {
        val navController = this.findNavController(R.id.navHostFragment)
        return navController.navigateUp()
    }
}
```

and That’s it. Sampai disini kita sudah berhasil membuat navigation dengan Navigation Component di Android sekaligus handling backstacknya. Masih 
ada banyak fitur dari Navigation Component seperti menambahkan conditional navigation, menyimpan data, menambahkan animation dll. Jadi silahkan di explore 
ya , linknya bisa dilihat [disini](https://developer.android.com/guide/navigation).

{% include disqus.html %}

