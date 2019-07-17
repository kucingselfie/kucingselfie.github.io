---
title: "Android : Fetching Data The Jetpack Way"
tags: [kotlin, android, jetpack]
comments: true
---

Apa itu fetch data ? Fetch merupakan fungsi dasar untuk meminta sumber daya melalui jaringan, 
Secara dasar berhubungan dengan request & response(permintaan/tanggapan). Saya ambil di postnya bapak Rin [disini](https://medium.com/@ri7nz/3-cara-fetch-data-di-reactjs-aaa064ccf5e9) wkwk. 
Kali ini kita akan belajar melakukan fetch data di Android dengan component Android Jetpack. Yang belum tahu Android Jetpack silahkan baca dulu postingan saya sebelum ini. Pertama yang perlu kita persiapkan adalah sebuah project, disini saya akan membuat sebuah project yang menampilkan list film populer. 
Saya mengambil data dari sebuah website film yang bernama [TMDB](https://www.themoviedb.org/). Anunya kira-kira seperti ini :

![anunya](https://i.ibb.co/V3kXPhG/Screen-Shot-2019-07-18-at-00-56-57.png)

Kita pakai arsitektur MVVM (Model View ViewModel). Model adalah representasi data yang digunakan biasanya berupa POJO di Java atau data classes di Kotlin. 
View adalah UI dari sebuah app yang bertugas menampilkan data. 
Sedangkan ViewModel tempat nyimpen Logic UI dan berinteraksi langsung ke View. Oke lanjut.

Kita buat dulu project di Android Studio, kemudian di build.gradle tambahin beberapa library dibawah, 
jangan lupa enable databinding :

```
// Navigation
implementation "android.arch.navigation:navigation-fragment-ktx:$version_navigation"
implementation "android.arch.navigation:navigation-ui-ktx:$version_navigation"

// ViewModel and LiveData
implementation "androidx.lifecycle:lifecycle-extensions:$version_lifecycle_extensions"
implementation "com.squareup.retrofit2:retrofit: $version_retrofit"
implementation "com.squareup.retrofit2:converter-moshi:$version_retrofit"
implementation "com.squareup.moshi:moshi:$version_moshi"
implementation "com.squareup.moshi:moshi-kotlin:$version_moshi"
implementation "org.jetbrains.kotlinx:kotlinx-coroutines-core:$version_kotlin_coroutines"
implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:$version_kotlin_coroutines"
implementation "com.jakewharton.retrofit:retrofit2-kotlin-coroutines-adapter:$version_retrofit_coroutines_adapter"
implementation "com.github.bumptech.glide:glide:$version_glide"
implementation "androidx.recyclerview:recyclerview:$version_recyclerview"
```

```
dataBinding {
    enabled = true
}
```

### Lifecycle Component
Didalam lifecycle component ada LiveData yang akan kita gunakan untuk melakukan observe data si Modelnya jadi ketika terjadi perubahan data maka akan langsung di update ke View nya.

### Retrofit dan Moshi
Retrofit digunakan untuk melakukan request data ke API. Sedangkan Moshi digunakan untuk melakukan parse data dari JSON ke Java atau Kotlin Object. 

Lanjut dulu aja deh. *wkwk

Buat model sesuai dengan response apinya. Disini saya menggunakan Movie.kt dan MovieResponse.kt 

```
data class Movie(
    val id: Int,
    val title: String,
    @Json(name="poster_path")
    val posterPath: String,
    val overview: String
)
```

```
data class MovieResponse(
    val page: Int,
    @Json(name="total_results")
    val totaResults: Int,
    @Json(name="total_pages")
    val totalPages: Int,
    val results: MutableList<Movie>
)
```

Buat file dengan nama Apiservice.kt isinya adalah :

Base Url dari Api kita
```
private const val BASE_URL = "https://api.themoviedb.org/3/"
```

inisialiasasi Moshi dan Retrofit
```
private val moshi = Moshi.Builder()
    .add(KotlinJsonAdapterFactory())
    .build()
```

```
private val retrofit = Retrofit.Builder()
    .addConverterFactory(MoshiConverterFactory.create(moshi))
    .addCallAdapterFactory(CoroutineCallAdapterFactory())
    .baseUrl(BASE_URL)
    .build()
```

pada .addCallAdapterFactory kita ubah metode Call dari retrofit dengan memakai Coroutine Call. 
Kita akan memakai Deferred dari Coroutine karena di dalamnya terdapat metode .await() yang berfungsi mengeksekusi 
Call secara Asyncronously istilah lainnya non-blocking UI dimana metode tersebut tidak akan mengganggu thread UI kita. 
Lanjut masih di file yang sama tambahkan kode dibawah.

```
interface Apiservice {
    @GET("movie/popular")
    fun getMovies(
        @Query("api_key") type: String
    ) : Deferred<MovieResponse>
}
```

Fungsi tersebut akan melakukan call sesuai dengan endpoint yang diberikan. 
Dibawahnya lagi kita buat sebuah object yang menginisialisasi Apiservice ke retrofit.

```
object Api {
    val retrofitService : Apiservice by lazy {
        retrofit.create(Apiservice::class.java)
    }
```

Pada layout activity_main, kita coba buat desain seperti ini :

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
    <TextView
            android:layout_width="match_parent" android:layout_height="wrap_content"
            android:text="Popular Movies"
            android:textColor="@color/colorPrimary"
            android:textSize="20sp"
            android:layout_marginTop="8dp"
            android:padding="8dp"/>
    <fragment
            xmlns:app="http://schemas.android.com/apk/res-auto"
            android:id="@+id/nav_host_fragment"
            android:name="androidx.navigation.fragment.NavHostFragment"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:defaultNavHost="true"
            app:navGraph="@navigation/nav_graph"/>

</LinearLayout>
```

Setelah itu kita buat fragment untuk membuat UI halaman depannya disini saya beri nama HomeFragment juga membuat ViewModel bernama HomeViewModel. 
Pada layout fragment_home kita tambahkan RecylerView untuk menampilkan List data.

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:tools="http://schemas.android.com/tools" xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto">
    <data>
        <variable name="viewModel" type="com.example.lambo.home.HomeViewModel"/>
    </data>

    <androidx.constraintlayout.widget.ConstraintLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            tools:context=".MainActivity">

        <androidx.recyclerview.widget.RecyclerView
                android:id="@+id/movie_grid"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                app:layoutManager="androidx.recyclerview.widget.GridLayoutManager"
                app:spanCount="2"
                tools:itemCount="16"
                android:padding="6dp"
            
                tools:listitem="@layout/grid_view_item"
                tools:layout_editor_absoluteX="8dp" tools:layout_editor_absoluteY="107dp"/>
    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```

Kita pindah ke HomeViewModel. Buat HomeViewModel extends sebagai ViewModel kemudian kita buat variabel LiveData yang berfungsi sebagai Observer

```
private val _movies = MutableLiveData<List<Movie>>()
val movies: LiveData<List<Movie>> get() = _movies
```

Lalu kita buat viewModelJob dan init ke Coroutine Scope 
```
private var viewModelJob = Job()
private val coroutineScope = CoroutineScope(viewModelJob + Dispatchers.Main)
```

Pastikan untuk mematikan Job ketika ViewModel sudah tidak digunakan lagi, dengan kata lain pada method onCleared(). 
Kemudian kita buat init getMovies() untuk melakukan fetch data ketika ViewModel pertama kali dijalankan.

```
override fun onCleared() {
    super.onCleared()
    viewModelJob.cancel()
}

init {
    getMovies()
}
```

Selanjutnya pada method getMovies() tambahkan kode ini :
```
coroutineScope.launch {
    val getMoviesDeferred = Api.retrofitService.getMovies(API_KEY)
    try {
        val listResult = getMoviesDeferred.await()
        _movies.value = listResult.results
    } catch (e: Exception) {
        _movies.value = ArrayList()
    }
}
```

Fetch data kita eksekusi di dalam scope coroutine untuk memastikan tidak ada thread yang terganggu ketika fetching data. 
Setelah itu kita update data LiveData sesuai dengan result dari apinya. 

Langkah selanjutnya adalah (waduh agak panjang ya) menambahkan adapter recyclerviewnya. 
Kita buat file bernama MovieAdapter, tambahkan kode berikut :

```
class MovieAdapter : ListAdapter<Movie, MovieAdapter.ViewHolder>(DiffCalback) {
    class ViewHolder(private var binding: GridViewItemBinding) : RecyclerView.ViewHolder(binding.root) {
        fun bind(movie: Movie) {
            binding.movie = movie
            binding.executePendingBindings()
        }
    }

    companion object DiffCalback : DiffUtil.ItemCallback<Movie>() {
        override fun areItemsTheSame(oldItem: Movie, newItem: Movie): Boolean {
            return oldItem === newItem
        }

        override fun areContentsTheSame(oldItem: Movie, newItem: Movie): Boolean {
           return oldItem.id == newItem.id
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        return ViewHolder(GridViewItemBinding.inflate(LayoutInflater.from(parent.context)))
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val movie = getItem(position)
        holder.bind(movie)
    }
}
```

Untuk layout itemnya kira-kira seperti ini hanya ada gambar dan judul filmnya :
```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        xmlns:app="http://schemas.android.com/apk/res-auto">

    <data>
        <variable name="movie" type="com.example.lambo.model.Movie"/>
    </data>

    <androidx.constraintlayout.widget.ConstraintLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">

        <ImageView
                android:id="@+id/movie_image"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:scaleType="fitCenter"
                android:adjustViewBounds="true"
                android:padding="2dp"
                tools:src="@tools:sample/backgrounds/scenic"
                android:layout_marginTop="8dp"
                app:layout_constraintTop_toTopOf="parent"/>

        <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                tools:text="Title"
                android:layout_marginTop="8dp"
                app:layout_constraintTop_toBottomOf="@+id/movie_image" app:layout_constraintStart_toStartOf="parent"
                android:layout_marginStart="8dp"
                app:layout_constraintEnd_toEndOf="parent"
                android:layout_marginEnd="8dp"/>

    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```

Adapter ini berfungsi untuk mengatur list itemnya mulai dari klik event, layout, bahkan ketika terjadi perubahan atau update data dari listnya. 
Untuk update data disini saya tidak menggunkan adapter.notifyDataSetChanged() , melainkan DiffUtil callback karena DiffUtil sendiri akan mengecek perubahan data dari listnya berdasarkan id yang diberikan dan ketika terjadi perubahan data maka dia hanya akan mengupdate pada current item yang berubah, 
sedangkan adapter.notifyDataSetChanged() akan melakukan draw ulang seluruh data yang mungkin akan memerlukan lebih banyak waktu dalam proses update data sebuah recyclerview.

Setelah jadi kita buat sebuah file Binding Adapter yaitu adapter yang terhubung langsung dengan data binding, yang akan menyajikan data ke layoutnya. Nama filenya BindingAdapter.kt.

Pertama kita buat variable untuk base url imagenya
```
val BASEURL_IMAGE = https://image.tmdb.org/t/p/w185/
```

Kemudian buat fungsi untuk melakukan binding data ke recyclerView
```
@BindingAdapter("listData")
fun bindRecyclerView(recyclerView: RecyclerView, data: List<Movie>?) {
    val adapter = recyclerView.adapter as MovieAdapter
    adapter.submitList(data)
}
```

Selanjutnya adalah fungsi binding gambar dan text pada item recyclerviewnya
```
@BindingAdapter("imageUrl")
fun bindImage(imgView: ImageView, imgUrl: String?) {
    imgUrl?.let {
        Glide.with(imgView.context)
            .load(BASEURL_IMAGE + it)
            .apply(
                RequestOptions()
                    .placeholder(R.drawable.ic_image))
            .into(imgView)
    }
}

@BindingAdapter("textTitle")
fun bindText(textView: TextView, text: String?) {
    text?.let {
        textView.text = it
    }
}
```

Setelah itu, kita loncat dulu ke HomeFragment, pada HomeFragment kita hanya perlu melakukan binding adapter RecyclerView dan ViewModelnya saja
```
val binding = FragmentHomeBinding.inflate(inflater)

binding.lifecycleOwner = this
binding.viewModel = viewModel
binding.movieGrid.adapter = MovieAdapter()
return binding.root
```

Langkah terakhir adalah update data pada layoutnya, masuk ke fragment_home. 
Untuk menampilkan list data pada UI, kita tambahkan binding expression pada recyclerView
```
app:listData="@{viewModel.movies}"
```

Sedangkan untuk gambar dan judul filmnya, kita update di layout grid_view_item
```
app:imageUrl="@{movie.posterPath}"
```
```
app:textTitle="@{movie.title}"
```

Dan akan terjadi kemunculan yang seperti ini :

![Fetchdata](https://i.ibb.co/yXjLcn8/Screen-Shot-2019-07-18-at-03-45-27.png "Kemunculan Lambo")

Sampai disini kita sudah berhasil melakukan fetch data sekaligus menampilkan data dengan Jetpack Component, Retrofit dan Kotlin Coroutine di Android, mungkin ada beberapa atau banyak kekurangan pada tulisan ini, untuk itu mohon dimaklumi karena penulis juga seorang nup yang hanya mencoba sharing. 
Yang mau sourcenya bisa dilihat di github [saya](https://github.com/dzalif/lambo). Thank you and Keep ~~Bucin~~ Learning :)

{% include disqus.html %}





