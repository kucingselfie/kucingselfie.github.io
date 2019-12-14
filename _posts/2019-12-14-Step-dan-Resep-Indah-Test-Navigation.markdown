---
title: "Step dan Resep indah to Test your Navigation Component"
tags: [kotlin, android, testing]
comments: true
---

Halo bapak ibu, pada kesempatan kali ini saya mau share sedikit tentang bagaimana melakukan Instrumented Test pada Navigation Component sebagaimana yang telah kita ketahui bersama bapak ibu, 
bahwa aplikasi yang bagus adalah aplikasi yang bisa di tes. 
Untuk yang belum tahu di Android sendiri ada 2 jenis Automated Unit Test :

- <b>Local Test</b> yaitu test yang berjalan dan di compile ke lokal mesin kalian, dalam hal ini adalah JVM (Java Virtual Machine), biasanya digunakan untuk melakukan function test pada aplikasi.
- <b>Instrumented Test</b> yaitu test yang berjalan pada Android device atau emulator dan biasanya digunakan untuk melakukan UI test.
Nah, karena navigation component berhubungan langsung dengan UI maka kita akan coba menerapkan Instrumented Test. 
Oiya, yang belum familiar dengan navigation component boleh baca post saya [disini](https://kucingselfie.com/2019/07/07/Belajar-Navigation-Component/)

*"Let's get into code"*


Pertama setup dulu project dan navigationnya di android studio, cara setupnya bisa post saya diatas ya. 
Buat dua screen yaitu screen list movie dan detail movie. Setup project tree nya seperti ini. 

![directory](https://i.ibb.co/hBPsvvn/Screen-Shot-2019-12-14-at-19-46-42.png)

Untuk membuat fragment klik <b>New - Fragment - Fragment (with ViewModel)</b>. 
Maka otomatis si Android Studionya akan melakukan generate fragment dan ViewModel. 
Step ini sangat berguna untuk kalian yang pakai arsitektur MVVM sehingga kita tidak perlu susah payah membuat class ViewModel nya. Berikan nama ListMovieFragment atau apapun. 
Setelah itu lakukan step yang sama untuk membuat <b>DetailMovieFragment</b>.

Buatlah model untuk <b>Movie</b> dan <b>DetailMovie</b>

```
data class Movie(
   val id: Int,
   val title: String,
   val description: String,
   val image: Int
)
```

```
@Parcelize
data class DetailMovie(
   val id: Int,
   val title: String,
   val description: String,
   val image: Int
) : Parcelable
```

Pada <b>DetailMovie</b> saya menggunakan <b>Parcelable</b> yang digunakan untuk mengirim object antar fragment 
karena nantinya data dari <b>ListMovieFrament</b> akan dikirim ke <b>DetailMovieFragment</b>. 
Selanjutnya buat UI <b>list_movie_fragment</b>, <b>item_movie</b> dan <b>detail_movie_fragment</b>

<b>list_movie_fragment</b>


```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
   xmlns:app="http://schemas.android.com/apk/res-auto"
   xmlns:tools="http://schemas.android.com/tools">
   <data>
       <variable
           name="viewmodel"
           type="com.kucingselfie.navigationtest.ui.movie.list.ListMovieViewModel" />
   </data>
   <androidx.constraintlayout.widget.ConstraintLayout
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       android:background="@android:color/white"
       tools:context=".ui.movie.MovieFragment">

       <androidx.recyclerview.widget.RecyclerView
           android:id="@+id/rvMovie"
           android:layout_width="match_parent"
           android:layout_height="match_parent"
           tools:listitem="@layout/item_movie"
           app:layoutManager="androidx.recyclerview.widget.StaggeredGridLayoutManager"
           app:listMovie="@{viewmodel.getMovies}"
           app:spanCount="2"
           app:layout_constraintBottom_toBottomOf="parent"
           app:layout_constraintEnd_toEndOf="parent"
           app:layout_constraintStart_toStartOf="parent"
           app:layout_constraintTop_toTopOf="parent" />

   </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```

<b>item_movie</b>

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
   xmlns:app="http://schemas.android.com/apk/res-auto"
   xmlns:tools="http://schemas.android.com/tools">
   <data>
       <variable
           name="model"
           type="com.kucingselfie.navigationtest.model.Movie" />
   </data>
      <androidx.cardview.widget.CardView
       android:layout_width="match_parent"
       android:layout_height="wrap_content"
       app:cardUseCompatPadding="true"
       android:foreground="?android:attr/selectableItemBackground"
       android:clickable="true"
       app:cardCornerRadius="4dp"
       app:cardElevation="4dp"
       android:focusable="true">
       <androidx.constraintlayout.widget.ConstraintLayout
           android:layout_width="match_parent"
           android:layout_height="wrap_content"
           android:padding="16dp">
           <ImageView
               android:id="@+id/imageView"
               android:layout_width="match_parent"
               android:layout_height="150dp"
               app:bindImage="@{model.image}"
               app:layout_constraintEnd_toEndOf="parent"
               app:layout_constraintStart_toStartOf="parent"
               app:layout_constraintTop_toTopOf="parent"
               tools:srcCompat="@tools:sample/avatars" />

           <TextView
               android:id="@+id/textView"
               android:layout_width="wrap_content"
               android:layout_height="wrap_content"
               android:layout_marginTop="16dp"
               android:text="@{model.title}"
               android:textColor="@android:color/black"
               android:textSize="16sp"
               android:textStyle="bold"
               app:layout_constraintEnd_toEndOf="@+id/imageView"
               app:layout_constraintStart_toStartOf="@+id/imageView"
               app:layout_constraintTop_toBottomOf="@+id/imageView"
               tools:text="Title" />

           <TextView
               android:id="@+id/textView2"
               android:layout_width="wrap_content"
               android:layout_height="wrap_content"
               android:layout_marginTop="8dp"
               android:text="@{model.description}"
               app:layout_constraintEnd_toEndOf="@+id/imageView"
               app:layout_constraintStart_toStartOf="@+id/imageView"
               app:layout_constraintTop_toBottomOf="@+id/textView"
               tools:text="Description" />
       </androidx.constraintlayout.widget.ConstraintLayout>
   </androidx.cardview.widget.CardView>
</layout>
```

<b>detail_movie_fragment</b>

```
<?xml version="1.0" encoding="utf-8"?>
<layout>
   <data>
       <variable
           name="model"
           type="com.kucingselfie.navigationtest.model.DetailMovie" />
   </data>
   <androidx.constraintlayout.widget.ConstraintLayout
       xmlns:android="http://schemas.android.com/apk/res/android"
       xmlns:app="http://schemas.android.com/apk/res-auto"
       xmlns:tools="http://schemas.android.com/tools"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       android:padding="16dp">

       <ImageView
           android:id="@+id/image"
           android:layout_width="150dp"
           android:layout_height="200dp"
           android:layout_marginTop="16dp"
           app:bindImage="@{model.image}"
           app:layout_constraintEnd_toEndOf="parent"
           app:layout_constraintStart_toStartOf="parent"
           app:layout_constraintTop_toTopOf="parent"
           tools:src="@tools:sample/avatars" />

       <TextView
           android:id="@+id/tvTitle"
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:layout_marginTop="16dp"
           android:text="@{model.title}"
           tools:text="Title"
           android:textStyle="bold"
           android:textSize="16sp"
           app:layout_constraintEnd_toEndOf="@+id/image"
           app:layout_constraintStart_toStartOf="@+id/image"
           app:layout_constraintTop_toBottomOf="@+id/image" />

       <TextView
           android:id="@+id/tvDescription"
           android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:layout_marginTop="8dp"
           android:text="@{model.description}"
           tools:text="Description"
           app:layout_constraintEnd_toEndOf="@+id/image"
           app:layout_constraintStart_toStartOf="@+id/image"
           app:layout_constraintTop_toBottomOf="@+id/tvTitle" />

   </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```

Buat folder util dan buat sebuah object <b>DataDummy</b> untuk menampung data film yang akan kita tampilkan

```
object DataDummy {
   fun generateMovies(): List<Movie> {
       val movies = mutableListOf<Movie>()
       movies.add(
           Movie(
               0,
               "Alita: Battle Angle",
               "When Alita awakens with no memory of who she is in a future world she does not recognize, she is taken in by Ido, a compassionate doctor who realizes that somewhere in this abandoned cyborg shell is the heart and soul of a young woman with an extraordinary past.",
               R.drawable.poster_alita
           )
       )
       movies.add(
           Movie(
               1,
               "Aquaman",
               "Once home to the most advanced civilization on Earth, Atlantis is now an underwater kingdom ruled by the power-hungry King Orm. With a vast army at his disposal, Orm plans to conquer the remaining oceanic people and then the surface world. Standing in his way is Arthur Curry, Orm's half-human, half-Atlantean brother and true heir to the throne.",
               R.drawable.poster_aquaman
           )
       )
       movies.add(
           Movie(
               2,
               "Bohemian Rhapsody",
               "Singer Freddie Mercury, guitarist Brian May, drummer Roger Taylor and bass guitarist John Deacon take the music world by storm when they form the rock 'n' roll band Queen in 1970. Hit songs become instant classics. When Mercury's increasingly wild lifestyle starts to spiral out of control, Queen soon faces its greatest challenge yet – finding a way to keep the band together amid the success and excess.",
               R.drawable.poster_bohemian
           )
       )
       movies.add(
           Movie(
               3,
               "Cold Pursuit",
               "The quiet family life of Nels Coxman, a snowplow driver, is upended after his son's murder. Nels begins a vengeful hunt for Viking, the drug lord he holds responsible for the killing, eliminating Viking's associates one by one. As Nels draws closer to Viking, his actions bring even more unexpected and violent consequences, as he proves that revenge is all in the execution.",
               R.drawable.poster_cold_persuit
           )
       )
       movies.add(
           Movie(
               4,
               "Creed",
               "The former World Heavyweight Champion Rocky Balboa serves as a trainer and mentor to Adonis Johnson, the son of his late friend and former rival Apollo Creed.",
               R.drawable.poster_creed
           )
       )
       movies.add(
           Movie(
               5,
               "Glass",
               "In a series of escalating encounters, former security guard David Dunn uses his supernatural abilities to track Kevin Wendell Crumb, a disturbed man who has twenty-four personalities. Meanwhile, the shadowy presence of Elijah Price emerges as an orchestrator who holds secrets critical to both men.",
               R.drawable.poster_glass
           )
       )
       movies.add(
           Movie(
               6,
               "How to Train Your Dragon",
               "As Hiccup fulfills his dream of creating a peaceful dragon utopia, Toothless’ discovery of an untamed, elusive mate draws the Night Fury away. When danger mounts at home and Hiccup’s reign as village chief is tested, both dragon and rider must make impossible decisions to save their kind.",
               R.drawable.poster_how_to_train
           )
       )
       movies.add(
           Movie(
               7,
               "Robin Hood",
               "A war-hardened Crusader and his Moorish commander mount an audacious revolt against the corrupt English crown.",
               R.drawable.poster_robin_hood
           )
       )
       movies.add(
           Movie(
               8,
               "Spiderman into The Spider Verse",
               "Miles Morales is juggling his life between being a high school student and being a spider-man. When Wilson \"Kingpin\" Fisk uses a super collider, others from across the Spider-Verse are transported to this dimension.",
               R.drawable.poster_spiderman
           )
       )
       movies.add(
           Movie(
               9,
               "Avengers: Infinity War",
               "As the Avengers and their allies have continued to protect the world from threats too large for any one hero to handle, a new danger has emerged from the cosmic shadows: Thanos. A despot of intergalactic infamy, his goal is to collect all six Infinity Stones, artifacts of unimaginable power, and use them to inflict his twisted will on all of reality. Everything the Avengers have fought for has led up to this moment - the fate of Earth and existence itself has never been more uncertain.",
               R.drawable.poster_infinity_war
           )
       )
       return movies
   }
}
```

Selanjutnya pada <b>ListMovieViewModel</b> kita ambil data dari si data dummy

```
 fun getMovies() : List<Movie> {
       return generateMovies()
   }
```

Buat file <b>BindingAdapter</b>, file ini digunakan untuk membuat custom xml prefix dari databinding yang nantinya akan digunakan untuk menampilkan data dari adapter 
dan menampilkan gambar dengan Glide.

```
@BindingAdapter("listMovie")
fun listMovie(recyclerView: RecyclerView, data: List<Movie>?) {
   val adapter = recyclerView.adapter as MovieAdapter
   adapter.submitList(data)
}

@BindingAdapter("bindImage")
fun bindImage(imgView: ImageView, imgUrl: Int) {
   Glide.with(imgView.context)
       .load(imgUrl)
       .into(imgView)
}
```

Kemudian buka file <b>ListMovieFragment</b> untuk menampilkan datanya dan mengarahkan ke halaman detail, gunakan kode berikut :

```
class ListMovieFragment : Fragment() {

   private lateinit var viewModel: ListMovieViewModel

   private lateinit var binding: ListMovieFragmentBinding

   private lateinit var adapter: MovieAdapter

   override fun onCreateView(
       inflater: LayoutInflater, container: ViewGroup?,
       savedInstanceState: Bundle?
   ): View? {
       binding = ListMovieFragmentBinding.inflate(inflater)
       return binding.root
   }

   override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
       super.onViewCreated(view, savedInstanceState)
       viewModel = ViewModelProviders.of(this).get(ListMovieViewModel::class.java)
       binding.lifecycleOwner = this
       binding.viewmodel = viewModel
       adapter = MovieAdapter {
           val model = DetailMovie(
               it.id,
               it.title,
               it.description,
               it.image
           )
           val action = ListMovieFragmentDirections.actionMovieFragmentToDetailMovieFragment(model)
           findNavController().navigate(action)
       }
       binding.rvMovie.adapter = adapter
   }
}
```

Jangan lupa pada <b>DetailMovieFragment</b> juga

```
class DetailMovieFragment : Fragment() {

   private lateinit var viewModel: DetailMovieViewModel

   override fun onCreateView(
       inflater: LayoutInflater, container: ViewGroup?,
       savedInstanceState: Bundle?
   ): View? {
       val binding = DetailMovieFragmentBinding.inflate(inflater)
       val model = DetailMovieFragmentArgs.fromBundle(arguments!!).model
       binding.model = model
       return binding.root
   }

   override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
       super.onViewCreated(view, savedInstanceState)
       viewModel = ViewModelProviders.of(this).get(DetailMovieViewModel::class.java)
   }
}
```

Sampai disini kita sudah bisa menjalankan appnya

![appnya](https://i.ibb.co/MRJWCcH/Screen-Shot-2019-12-14-at-21-43-35.png)

Sekarang kita masuk ke bagian testingnya. Ubah tampilan directory ke mode Project (panel kiri Android Studio) kemudian buat folder <b>debug/java/{namaPackage}/testing</b> dan didalamnya lagi buat file bernama <b>SingleFragmentActivity</b>. 
Buat juga file <b>AndroidManifest.xml</b> sejajar dengan directory java.

![directorynya](https://i.ibb.co/cX26kXK/Screen-Shot-2019-12-15-at-00-38-08.png)

```
class SingleFragmentActivity : AppCompatActivity() {
   override fun onCreate(savedInstanceState: Bundle?) {
       super.onCreate(savedInstanceState)
       val content = FrameLayout(this)
       val params = FrameLayout.LayoutParams(
           FrameLayout.LayoutParams.MATCH_PARENT, FrameLayout.LayoutParams.MATCH_PARENT,
           Gravity.CENTER
       )
       content.layoutParams = params
       content.id = R.id.container

       setContentView(content)
   }

   fun setFragment(fragment: Fragment) {
       supportFragmentManager
           .beginTransaction()
           .add(R.id.container, fragment, "TEST")
           .commit()
   }

   fun replaceFragment(fragment: Fragment) {
       supportFragmentManager
           .beginTransaction()
           .add(R.id.container, fragment)
           .commit()
   }
}
```

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
   xmlns:tools="http://schemas.android.com/tools"
   package="com.kucingselfie.navigationtest">
   <uses-permission android:name="android.permission.INTERNET" />
   <application tools:ignore="GoogleAppIndexingWarning">
       <activity android:name=".testing.SingleFragmentActivity" />
   </application>
</manifest>
```

<b>SingleFragmentActivity</b> ini adalah activity dummy yang dibuat untuk melakukan attach terhadap fragment yang akan kita test, 
dan tentu saja <b>AndroidManifest</b> untuk mendaftarkan activitynya.

Langkah selanjutnya, buka <b>ListMovieFragment</b>, tekan <b>options + Enter</b> pada keyboard kemudian pilih <b>create test - klik OK dan pilih directory androidTest kemudian klik OK</b>, 
maka android studio akan membuat sebuah file UI test yaitu <b>ListMovieFragmentTest</b>. Sekarang kita coba buat testnya.

Skenario test untuk List Movie adalah :
- Memastikan list movie ditampilkan
- Memastikan listnya berjumlah 10
- Memastikan list movie diarahkan ke halaman detail movie

Masuk ke directory <b>androidTest/{namaPackage}</b> kemudian buat directory bernama util dan didalamnya buat file bernama <b>RecyclerViewItemCountAssertion</b>.

```
class RecyclerViewItemCountAssertion(private var expectedCount: Int) : ViewAssertion {
   override fun check(view: View?, noViewFoundException: NoMatchingViewException?) {
       if (noViewFoundException != null) {
           throw noViewFoundException
       }
       val recyclerView = view as RecyclerView
       val adapter = recyclerView.adapter
       assertNotNull(adapter)
       assertThat(adapter?.itemCount, `is`(expectedCount))
   }
}
```

File ini digunakan untuk mengecek jumlah item pada RecyclerView.

Buka <b>ListMovieFragmentTest</b> . Pertama tambahkan test rule untuk menginisialisasi Activity dan melakukan attach ke fragment.

```
@Rule
@JvmField
var activityRule: ActivityTestRule<SingleFragmentActivity> = ActivityTestRule(SingleFragmentActivity::class.java)
private val movieFragment = ListMovieFragment()

@Before
fun setUp() {
   activityRule.activity.setFragment(movieFragment)
}
```

Kemudian kita buat test untuk mengecek apakah list ditampilkan dan jumlahnya 10.

```
@Test
fun checkIsDisplayed() {
   onView(withId(R.id.rvMovie)).check(matches(isDisplayed()))
   onView(withId(R.id.rvMovie)).check(RecyclerViewItemCountAssertion(10))
}
```

Lalu kita buat lagi test untuk memastikan list movie diarahkan ke halaman detail.

```
@Test
fun toDetailMovie() {
   val mock = mock(NavController::class.java)
   val scenario = launchFragmentInContainer<ListMovieFragment>()
   scenario.onFragment {
       Navigation.setViewNavController(it.requireView(), mock)
   }
   onView(withId(R.id.rvMovie)).perform(RecyclerViewActions.actionOnItemAtPosition<RecyclerView.ViewHolder>(0, click()))
   verify(mock).navigate(ListMovieFragmentDirections.actionMovieFragmentToDetailMovieFragment(
       DetailMovie(
           0,
           "Alita: Battle Angle",
           "When Alita awakens with no memory of who she is in a future world she does not recognize, she is taken in by Ido, a compassionate doctor who realizes that somewhere in this abandoned cyborg shell is the heart and soul of a young woman with an extraordinary past.",
           R.drawable.poster_alita
       )
   ))
}
```

Untuk melakukan test navigation kita menggunakan fragment scenario. Fragment scenario digunakan untuk membuka fragment pada mode testing, hal yang menarik adalah fragment scenario juga dapat digunakan untuk melakukan manajemen atau perubahan lifecycle state dari fragment. 
Nah, sampai disini kita sudah berhasil membuat test pada list movie, jalankan testnya jika berhasil maka akan muncul penampakan sosok berikut ini.

![test movie list](https://i.ibb.co/RpXn6Rs/Screen-Shot-2019-12-14-at-23-19-26.png)

Terakhir adalah menambahkan test pada <b>DetailMovieFragment</b> silahkan generate file <b>DetailMovieFragmentTest</b> dan tambahkan kode berikut

```
@RunWith(AndroidJUnit4::class)
class DetailMovieFragmentTest {
   private val dummyMovies = generateMovies()[0]
   @Test
   fun openDetailFragment() {
       val model = DetailMovie(
           0,
           "Alita: Battle Angle",
           "When Alita awakens with no memory of who she is in a future world she does not recognize, she is taken in by Ido, a compassionate doctor who realizes that somewhere in this abandoned cyborg shell is the heart and soul of a young woman with an extraordinary past.",t 
           R.drawable.poster_alita
       )
       val args = Bundle().apply {
           putParcelable("model", model)
       }
       launchFragmentInContainer<DetailMovieFragment>(args)
       onView(withId(R.id.tvTitle)).check(matches(isDisplayed()))
       onView(withId(R.id.tvTitle)).check(matches(withText(dummyMovies.title)))
   }
}
```

Skenario testnya adalah memastikan datanya ditampilkan dan mengecek datanya sudah sesuai. 
Silahkan jalankan kembali, jika berhasil maka kelarlah urusan kita untuk membuat Instrumented Test pada navigation component. 

Thanks and CMIIW.

Beberapa link sebagai referensi pada postingan ini : 

https://developer.android.com/training/basics/fragments/testing

https://developer.android.com/reference/androidx/fragment/app/testing/FragmentScenario

Dan source codenya boleh [kesini]()

{% include disqus.html %}


