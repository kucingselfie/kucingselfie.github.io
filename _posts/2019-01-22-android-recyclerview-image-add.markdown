---
title: "Android - Add Pokemon Button to RecyclerView"
tags: [android]
---


Maksudnya apa sih haha. Pertama RecyclerView adalah salah satu fitur di android yang memungkinkan kita untuk membuat list data secara dinamis, 
dari kata recyclerview saja kita cukup bisa mengartikan bahwa recyclerview adalah “pengulangan view”. 
Jadi kita hanya membuat satu layout item dan dapat digunakan berulang-ulang. Implementasinya biasanya digunakan untuk pembuatan layout list atau group list. 
Nah, sekarang masuk ke kasus saya, kemarin saya agak dapat masalah saat mengerjakan sebuah list tapi ingin menambahkan sebuah tombol di belakangnya. 
Sebenarnya mirip dengan implementasi “read more” pada vertical list, tetapi di kasus saya sendiri adalah GridView, GridView itu semacam list tapi bentuknya kotak-kotak hehe, 
kayak di aplikasi gallery lah. Jadi, saya mau menambahkan tombol add gambar di akhir listnya, sebenarnya ini maksud dari judul saya diatas lol. Langsung saja.

Import dulu :

```
implementation 'com.android.support:recyclerview-v7:28.0.0'
implementation 'com.android.support:cardview-v7:28.0.0'
```

Sebenarnya recyclerview saja sudah cukup, tapi disini saya juga masukin card view untuk bikin card di androidnya. 
Setelah itu attach recyclerview ke xmlnya, jangan lupa id nya.

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
    
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recycler_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
    </android.support.v7.widget.RecyclerView>

</android.support.constraint.ConstraintLayout>
```

Bind viewnya ke java

```
recyclerView = findViewById(R.id.recycler_view);
```

Kemudian kita coba buat adapter recyclerviewnya. 

```
public class PokemonAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {
    // View Types
    private static final int ITEM = 0;
    private static final int BUTTON = 1;

    private Context context;
    private List<Pokemon> pokemons;

    public PokemonAdapter(Context context, List<Pokemon> pokemons) {
        this.context = context;
        setListItem(pokemons);
    }

    public void setListItem(List<Pokemon> listItem) {
        this.pokemons = new ArrayList<>();
        pokemons.addAll(listItem);
    }

    @NonNull
    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int viewType) {
        LayoutInflater inflater = LayoutInflater.from(viewGroup.getContext());

        View viewItem = null;

        if (viewType == ITEM) {
            viewItem = inflater.inflate(R.layout.pokemon_item, viewGroup, false);
        } else if (viewType == BUTTON) {
            viewItem = inflater.inflate(R.layout.pokemon_item_add, viewGroup, false);
        }

        return new PokemonAdapter.ViewHolder(viewItem);
    }

    @Override
    public void onBindViewHolder(@NonNull RecyclerView.ViewHolder viewHolder, int position) {
        if (position == pokemons.size()) {
            final PokemonAdapter.ViewHolder itemVH = (PokemonAdapter.ViewHolder) viewHolder;
        } else {
            Pokemon current = pokemons.get(position);
            final PokemonAdapter.ViewHolder itemVH = (PokemonAdapter.ViewHolder) viewHolder;
            itemVH.pokemonImage.setBackgroundResource(current.getPicture());
            itemVH.pokemonName.setText(current.getName());
        }
    }

    @Override
    public int getItemCount() {
        return pokemons.size() + 1;
    }

    @Override
    public int getItemViewType(int position) {
        if (position == pokemons.size()) {
            return BUTTON;
        } else {
            return ITEM;
        }
    }

    public class ViewHolder extends RecyclerView.ViewHolder {
        ImageView pokemonImage;
        ImageView pokemonAdd;
        TextView pokemonName;
        public ViewHolder(View view) {
            super(view);
            pokemonImage = view.findViewById(R.id.pokemon_item);
            pokemonAdd = view.findViewById(R.id.pokemon_item_add);
            pokemonName = view.findViewById(R.id.pokemon_name);
        }
    }
}
```

Diatas ada layout pokemon_item dan pokemon_item_add, yang dimana saya mendefinisikan 2 layout sekaligus dalam satu adapter,
ini disebut recyclerview multiple viewtype, ini mungkin akan saya bahas lebih lagi nanti pada artikel lain. Kemudian untuk menambahkan satu view/button lagi di paling belakang listnya, kita ambil
method getItemViewType() untuk membedakan tiap item viewnya dan total size nya kita tambahkan dengan 1. Dengan cara ini kita sudah bisa
menerapkan view sesuai dengan kasus diatas.

Untuk kedua layout item view nya bisa dilihat disini :

Layout item pertama (list pokemon)
```
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.CardView
    android:padding="8dp"
    android:layout_margin="8dp"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="200dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="16dp"
        android:orientation="vertical">
        <ImageView
            android:id="@+id/pokemon_item"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:adjustViewBounds="true"
            android:background="@drawable/eevee"/>

        <TextView
            android:id="@+id/pokemon_name"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="name"/>
    </LinearLayout>

</android.support.v7.widget.CardView>
```
Hasilnya seperti ini. Agak gepeng tapi jangan khawatir semua akan baik-baik saja pada akhirnya hehe <br /> <br />
![Android](https://i.ibb.co/Wgtnrs3/Screen-Shot-2019-01-23-at-1-13-55-AM.png "List item pokemon")

Layout item tambah pokemon:
```
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.CardView
    android:padding="8dp"
    android:layout_margin="8dp"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="200dp">

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="center">
        <ImageView
            android:id="@+id/pokemon_item_add"
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:layout_gravity="center"
            android:adjustViewBounds="true"
            android:background="@drawable/add_pokemon_icon"/>

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:textSize="16sp"
            android:layout_marginTop="70dp"
            android:text="Tambah Pokemon"/>
    </FrameLayout>

</android.support.v7.widget.CardView>
```

![Android](https://i.ibb.co/mySrdns/Screen-Shot-2019-01-23-at-1-14-18-AM.png "Item tambah pokemon")

Jangan lupa buat model Pokemon nya :

```
public class Pokemon {
    int id;
    String name;
    private int picture;

    public Pokemon(String name, int picture) {
        this.name = name;
        this.picture = picture;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getPicture() {
        return picture;
    }

    public void setPicture(int picture) {
        this.picture = picture;
    }
}
```

Setelah itu kita kembali ke MainActivity untuk menambahkan datanya :
```
public class MainActivity extends AppCompatActivity {

    RecyclerView recyclerView;
    GridLayoutManager gridLayoutManager;

    PokemonAdapter adapter;
    List<Pokemon> pokemons = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        recyclerView = findViewById(R.id.recycler_view);

        initRecycler();
        initData();
    }

    private void initData() {
        Pokemon pokemon = new Pokemon("Bulbasaur", R.drawable.bulbasaur);
        pokemons.add(pokemon);
        pokemon = new Pokemon("Pikachu", R.drawable.pikachu);
        pokemons.add(pokemon);
        pokemon = new Pokemon("Eevee", R.drawable.eevee);
        pokemons.add(pokemon);
        pokemon = new Pokemon("Psyduck", R.drawable.psyduck);
        pokemons.add(pokemon);

        adapter.setListItem(pokemons);
        adapter.notifyDataSetChanged();
    }

    private void initRecycler() {
        //Recyclerview
        recyclerView.setHasFixedSize(true);
        recyclerView.setNestedScrollingEnabled(false);
        gridLayoutManager = new GridLayoutManager(this, 2);
        recyclerView.setItemAnimator(new DefaultItemAnimator());
        recyclerView.setLayoutManager(gridLayoutManager);

        adapter = new PokemonAdapter(this, new ArrayList<Pokemon>());
        recyclerView.setAdapter(adapter);
    }
}
```
Dan Gotcha, selamat kita sudah berhasil menambahkan ReyclerView sekaligus tombol add dengan metode multiple view type
pada RecyclerView.

![Android](https://i.ibb.co/kSjQ8m9/Screen-Shot-2019-01-23-at-12-54-51-AM.png "Final preview")

Link github project [disini](https://github.com/dzalif/add-pokemon-button)

Thank you :)



