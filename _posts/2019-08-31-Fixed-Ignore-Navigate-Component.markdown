---
title: "Fixed Error Ignoring navigate() call: FragmentManager has already saved its state Navigation Component"
tags: [kotlin, android]
comments: true
---

Halo, pada kesempatan kali ini saya akan coba share sedikit tentang case yang saya alami kemarin. 
Jadi, kemarin saya ada task yaitu melakukan scheduling Logout secara otomatis dalam waktu tertentu ketika device dalam kondisi Idle atau 
bahasa familiarnya mungkin ketika device sedang mengalami sleep. Pada android sendiri ada beberapa kondisi device tersebut dikatakan Idle :

1.	Ketika app sedang berjalan kemudian tombol Home ditekan.
2.	Ketika tombol recent app ditekan.
3.	Ketika device dalam keadaan ke-lock atau tombol lockscreen ditekan.

Salah satu teman saya merekomendasikan link [ini](http://www.devexchanges.info/2016/06/android-tip-detect-user-inactivity-auto.html) dan it’s work ketika digunakan pada activity. 
Case ini juga related dengan link stackoverflow [ini](https://stackoverflow.com/questions/51895243/ignoring-navigate-call-fragmentmanager-has-already-saved-its-state). 
Tetapi pada case saya sendiri, saya menggunakan navigation component maka saya coba dengan cara seperti ini :

-	Detecting User Inactivity
-	Add scheduling
-	Navigate when onPause() called

Maka kodenya akan seperti ini : 

```
override fun onPause() {
    super.onPause()
    timer = Timer()
    val logoutTask = logoutTask()
    timer?.schedule(logoutTask, 900000)
}
```

Pada lifecycle Android <strong>onPause()</strong> akan dipanggil ketika user inactive atau dalam keadaan sleep, maka saya taruh kodenya disitu dan scheduling untuk logout setelah 900.000 ms (15 menit). 
Setelah itu saya coba navigate di dalam <strong>onPause()</strong> seperti pada link yang saya baca diatas :

```
inner class LogoutTask: TimerTask() {
    override fun run() {
           navController.navigate(R.id.logoutFragment)
    }
}
```

Dan ketemu error :

```
Ignoring navigate() call: FragmentManager has already saved its state
```

Dia ngabaiin kode navigate saya karena katanya statenya udah kesave. 
Alhasil timerTask nya berhasil dijalankan tetapi tidak berpindah kemana-mana atau 
dalam hal ini saya tidak berpindah ke halaman logout. Ini terjadi mungkin karena navController tidak bisa dijalankan pada background task karena Activity dalam kondisi tidak visible. 
Maka saya coba dengan cara seperti ini :

-	Detect if device is idle and add state
-	Add scheduling in onPause()
-	Navigate when onResume() called

Maka kodenya akan berubah bentuk menjadi :

```
override fun onPause() {
    super.onPause()
    timer = Timer()
    val logoutTask = logoutTask()
    timer?.schedule(logoutTask, 90000)
}
```

```
inner class LogoutTask: TimerTask() {
    override fun run() {
           isAfterIdle = true
    }
}
```

Kemudian pada <strong>onResume()</strong> :

```
override fun onResume() {
    super.onResume()
    if (isAfterIdle) navController.navigate(R.id.loginFragment)
    timer?.cancel()
    timer = null
    isAfterIdle = false
}
```

and It’s worked! <strong>onResume()</strong> akan dipanggil ketika Activity berada di foreground, hal ini pun berhasil melakukan trigger pada navController. Selanjutnya tinggal tambahin satu function buat ngeclear sessionnya. 
Semoga bermanfaat ya bagi yang mengalami case yang sama. Thank you.

{% include disqus.html %}
