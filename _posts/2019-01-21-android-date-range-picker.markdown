---
title: "Android - Date Range Picker"
tags: [android]
---

Hi, pernah pake date picker di android ? date picker itu semacam input data tanggal yang biasanya dipakai untuk keperluan tertentu 
contoh pada kasus saya adalah buat ngefilter data berdasarkan tanggal, UI nya biasanya berupa kalender view di android. Diluar sana ada 
banyak sekali library buat date picker ataupun date range picker, tapi jika kalian masih belajar dan newbie kayak saya, boleh 
pakai yang satu ini, implementasinya cukup sederhana, tanpa interface callback dan dialog fragment.

Pertama import dulu librarynya. Siapapun yang sudah membuat library ini saya sangat berterima kasih karena telah menyelamatkan hidup saya :D

```
implementation 'com.github.subirZ:DateRangePicker:1.0'
```

Lalu buat layoutnya

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="16dp"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <Button
            android:id="@+id/open_calendar"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="8dp"
            android:text="@string/buka_kalender"/>

        <TextView
            android:id="@+id/start_date"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="16sp"
            android:text="@string/start_date"/>

        <TextView
            android:id="@+id/end_date"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="16sp"
            android:text="@string/end_date"/>

    </LinearLayout>
</android.support.constraint.ConstraintLayout>
```

Hasilnya seperti ini:

![Android](https://i.ibb.co/1G8G9c9/Screen-Shot-2019-01-21-at-6-28-22-PM.png "Android view")

Kemudian init id seperti biasa

```
openCalendar = findViewById(R.id.open_calendar);
        startDate = findViewById(R.id.start_date);
        endDate = findViewById(R.id.end_date);

        openCalendar.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                initCalendar();
            }
        });
```

Buat fungsi untuk manggil kalendernya :

```
private void initCalendar() {
        final DateRangePicker dateRangePicker = new DateRangePicker(this, new DateRangePicker.OnCalenderClickListener() {
            @SuppressLint("SetTextI18n")
            @Override
            public void onDateSelected(String s, String s1) {
               startDate.setText("Start Date : " + s);
               endDate.setText("End Date : " + s1);
            }
        });
        dateRangePicker.show();
        dateRangePicker.setBtnPositiveText("Oke");
        dateRangePicker.setBtnNegativeText("Batal");
    }
```

Dan udah jadi wkwk. Outputnya berupa start date dan end date yang dapat diolah sesuai kebutuhan :

![Android](https://i.ibb.co/phb3QjT/Screen-Shot-2019-01-21-at-12-11-01-AM.png "Android date range picker")

Meskipun postingan ini sangat sederhana, saya cukup susah payah mencari library yang se simple ini jadi tolong hargai saya ya hehe.
Noted : Library ini tidak bisa dijalankan di sdk 28 karena method Tablayoutnya sudah deprecated sama android support dan belum di update
oleh si pembuatnya, jadi jika ingin di implementasikan harus pakai compileSdkVersion 27 kebawah dan minSdkVersion 16.

Just Sharing. Thanks :)

