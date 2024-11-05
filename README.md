Nama: **Muhammad Abyasa Pratama**
NPM: **2306207663**
Kelas: **F**

1. _Stateless widget_ merupakan jenis elemen pada Flutter yang tidak memuat data-data tertentu dan tidak terpengaruh oleh perubahan internal, namun bisa terpengaruh oleh aksi eksternal pada _parent widget_-nya. _Stateless widget_ biasanya digunakan untuk elemen-elemen UI yang bersifat statis seperti _title_ aplikasi dan ikon/gambar. Sementara itu, _stateful widget_ merupakan jenis elemen yang bisa terpengaruh oleh aksi atau perubahan yang terjadi secara internal, misalnya seperti perubahan pada data. _Stateful widget_ biasanya digunakan untuk elemen-elemen UI yang bersifat interaktif/dinamis seperti _counter_ dan _input form_.

2. Berikut merupakan widget yang digunakan dalam proyek ini:    
   - `Scaffold`: Digunakan untuk mengatur _layout_ dasar dari _page_ aplikasi.
   - `ScaffoldMessenger`: Digunakan untuk menampilkan _snackbar_.
   - `Material`: _Container_ dasar yang mendefinisikan _layout_ dan _styling_ dari suatu komponen desain (misalnya mengatur _layout_ dari suatu _card_).
   - `MaterialApp`: Berfungsi untuk mengatur tema dan _title_ aplikasi serta memuat _widget_ yang mendefinisikan _home page_ aplikasi.
   - `AppBar`: _Container_ untuk membuat suatu _navigation bar_ yang memuat judul/nama aplikasi.
   - `Center`: _Container_ untuk menempatkan tiap _child_ pada posisi tengah secara horizontal.
   - `Column`: _Container_ untuk menyusun tiap _child_ dalam suatu ruang vertikal.
   - `Padding`: Berfungsi untuk memberikan _space_/ruang kosong pada empat posisi.
   - `GridView`: Container untuk menempatkan tiap _child_ dalam _grid_ dengan n kolom (sesuai _count_ yang didefinisikan).
   - `InkWell`: Berfungsi untuk memberikan efek `splash` jika _parent widget_ `Material` diklik.
   - `SizedBox`: Berfungsi untuk membuat suatu _box_ kosong dengan ukuran (width/height) yang didefinisikan.
   - `Text`: Berfungsi untuk menampilkan teks.
   - `Icon`: Berfungsi untuk menampilkan _icon-icon_ yang sudah didefinisikan dalam Flutter.
   - `MyApp`: Berfungsi sebagai _root widget_ dari aplikasi.
   - `MyHomePage`: Widget yang mendefinisikan _layout_ dan tampilan dari _home page_ aplikasi.
   - `ItemHomePage`: Widget yang mendefinisikan atribut yang digunakan `ItemCard`.
   - `ItemCard`: Widget yang mendefinisikan tampilan tiga _button card_ dalam aplikasi.

3. `const` merupakan jenis variabel yang memiliki nilai yang sudah terdefinisi saat _compile time_. Contohnya seperti variabel teks (string) atau angka yang hanya di-_assign_ sekali saja. Sementara itu, `final` merupakan jenis variabel yang nilainya terdefinisi saat _runtime_. Salah satu contohnya yaitu variabel _instance_ dari DateTime.

4. `setState()` merupakan fungsi yang dipanggil ketika terjadi perubahan internal pada `state` suatu _widget_. Maka dari itu,`setState()` berfungsi untuk memberitahukan kepada sistem Flutter agar melakukan _build_ ulang pada suatu/beberapa _widget_ untuk meng-_update_ tampilan aplikasi sesuai dengan _state_ terbaru. Variabel yang dipengaruhi oleh `setState()` harus didefinisikan di dalam _State class_ dari suatu _stateful widget_ dan digunakan dalam fungsi `Widget build()` (digunakan dalam tampilan aplikasi). Sementara itu, variabel-variabel yang memiliki nilai tetap seperti `const` dan `final` tidak bisa dipengaruhi oleh pemanggilan `setState()`.

5. Berikut merupakan langkah-langkah untuk mengimplementasikan _checklist_ Tugas 7:

Pertama-tama, saya melakukan inisiasi untuk membuat proyek Flutter baru bernama *e_commerce* dengan menjalankan `flutter create e_commerce` pada Terminal. Setelah itu, saya membuat repositori GitHub baru bernama _e-commerce-mobile_ dan menghubungkan direktori proyek lokal dengan repo GitHub tersebut.

Selanjutnya, di dalam direktori proyek (*e_commerce*), buatlah berkas baru di dalam direktori `lib` bernama `menu.dart`. Setelah itu, tambahkan kode berikut di dalam berkas `menu.dart`:
```
import 'package:flutter/material.dart';
```

Setelah itu, di dalam berkas `main.dart`, hapuslah keseluruhan dari `class _MyHomePageState` dan pindahkan keseluruhan dari `class MyHomePage` ke dalam berkas `menu.dart`. Agar `class MyHomePage` bisa tetap diakses dalam berkas `main.dart`, tambahkan kode berikut pada bagian atas berkas `main.dart`:
```
import 'package:e_commerce/menu.dart';
```

Dalam berkas main.dart, lakukan pengaturan pada tema aplikasi dengan mengatur warna pada variabel `colorScheme` di dalam `MaterialApp` seperti berikut:
```
colorScheme: ColorScheme.fromSwatch(
    primarySwatch: Colors.grey,
).copyWith(secondary: Colors.grey[300]),
```

Dalam berkas yang sama, ubahlah pemanggilan `MyHomePage` dari yang semula berupa `home: const MyHomePage(title: 'Flutter Demo Home Page')` menjadi `home: MyHomePage()`. Setelah itu, bukalah berkas `menu.dart` dan ubahlah sifat class `MyHomePage` menjadi stateless dengan mengganti `extends StatefulWidget` menjadi `extends StatelessWidget`. Hapuslah juga keseluruhan isi dari `class MyHomePage`. Kemudian, tambahkan kode berikut untuk membuat constructor dari class `MyHomePage`:
```
MyHomePage({super.key});
```

Selanjutnya, buatlah class baru bernama `ItemHomePage` untuk memuat atribut-atribut dari _button card_ yang akan dibuat:
```
class ItemHomepage {
    final String name;
    final IconData icon;
    final Color color;

    ItemHomepage(this.name, this.icon, this.color);
}
```
Setelah itu, buatlah list of ItemHomePage untuk membuat atribut dari button-button yang ingin dibuat pada class `MyHomePage`. Pastikan untuk menambahkan warna yang berbeda-beda untuk tiap _button_-nya:
```
final List<ItemHomepage> items = [
    ItemHomepage("Lihat Daftar Produk", Icons.search, Colors.red),
    ItemHomepage("Tambah Produk", Icons.add, Colors.green),
    ItemHomepage("Logout", Icons.logout, Colors.blue),
];
```

Selanjutnya, buatlah widget/class baru bernama `ItemCard` yang mendefinisikan suatu _button_ yang menggunakan atribut dari `ItemHomePage` dan menampilkan pesan _snackbar_ jika diklik:
```
class ItemCard extends StatelessWidget {
  // Menampilkan kartu dengan ikon dan nama.

  final ItemHomepage item;

  const ItemCard(this.item, {super.key});

  @override
  Widget build(BuildContext context) {
    return Material(
      // Menentukan warna background dari ItemHomePage.
      color: item.color,
      // Membuat sudut kartu melengkung.
      borderRadius: BorderRadius.circular(12),

      child: InkWell(
        // Aksi ketika kartu ditekan.
        onTap: () {
          // Menampilkan pesan SnackBar saat kartu ditekan.
          ScaffoldMessenger.of(context)
            ..hideCurrentSnackBar()
            ..showSnackBar(
              SnackBar(content: Text("Kamu telah menekan tombol ${item.name}"))
            );
        },
        // Container untuk menyimpan Icon dan Text
        child: Container(
          padding: const EdgeInsets.all(8),
          child: Center(
            child: Column(
              // Menyusun ikon dan teks di tengah kartu.
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                Icon(
                  item.icon,
                  color: Colors.white,
                  size: 30.0,
                ),
                const SizedBox(height: 10.0),
                const Padding(padding: EdgeInsets.all(3)),
                Text(
                  item.name,
                  textAlign: TextAlign.center,
                  style: const TextStyle(color: Colors.white),
                ),
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```

Kemudian, definisikan tampilan _home page_ aplikasi yang memuat _ItemCard_ yang sudah dibuat dengan menambahkan `Widget Build()` pada class `MyHomePage` seperti berikut:
```
    @override
    Widget build(BuildContext context) {
        return Scaffold(
          // AppBar adalah bagian atas halaman yang menampilkan judul.
          appBar: AppBar(
            // Judul aplikasi NetBuy.
            title: const Text(
              'NetBuy',
              style: TextStyle(
                color: Colors.white,
                fontWeight: FontWeight.bold,
              ),
            ),
            // Warna latar belakang AppBar diambil dari skema warna tema aplikasi.
            backgroundColor: Theme.of(context).colorScheme.primary,
          ),
          // Body halaman dengan padding di sekelilingnya.
          body: Padding(
            padding: const EdgeInsets.all(16.0),
            // Menyusun widget secara vertikal dalam sebuah kolom.
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.center,
              children: [
                // Menempatkan widget berikutnya di tengah halaman.
                Center(
                  child: Column(
                    // Menyusun teks dan grid item secara vertikal.

                    children: [
                      // Menampilkan teks sambutan dengan gaya tebal dan ukuran 18.
                      const Padding(
                        padding: EdgeInsets.only(top: 16.0),
                        child: Text(
                          'Welcome to NetBuy',
                          style: TextStyle(
                            fontWeight: FontWeight.bold,
                            fontSize: 18.0,
                          ),
                        ),
                      ),

                      // Grid untuk menampilkan ItemCard dalam bentuk grid 3 kolom.
                      GridView.count(
                        primary: true,
                        padding: const EdgeInsets.all(20),
                        crossAxisSpacing: 10,
                        mainAxisSpacing: 10,
                        crossAxisCount: 3,
                        // Agar grid menyesuaikan tinggi kontennya.
                        shrinkWrap: true,

                        // Menampilkan ItemCard untuk setiap item dalam list items.
                        children: items.map((ItemHomepage item) {
                          return ItemCard(item);
                        }).toList(),
                      ),
                    ],
                  ),
                ),
              ],
            ),
          ),
        );
    }
```
Terakhir, jalankan `flutter analyze` pada Terminal untuk memastikan program tidak memiliki isu dan jalankan `flutter run` untuk melihat tampilan aplikasi yang sudah dibuat.