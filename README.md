Nama: **Muhammad Abyasa Pratama**<br>
NPM: **2306207663**<br>
Kelas: **F**<br>

## Jawaban Tugas 9

1. Pembuatan model pada proyek Flutter dapat mempermudah kita dalam mengakses data yang diambil dari luar proyek (misal dari _web_) menggunakan JSON. Dengan mendefinisikan model, data JSON bisa langsung dilakukan _parsing_ dan _serialization_ dalam satu tempat. Penggunaan model juga dapat membantu menjaga konsistensi dari struktur data yang diakses. Hal ini dapat membantu kita untuk menghindari kasus pengaksesan field data yang salah sehingga bisa berujung pada _error_. Meskipun pengaksesan data dari JSON secara langsung tanpa melalui Model merupakan hal yang memungkinkan, penggunaan Model tetap merupakan hal yang dianjurkan untuk menjaga konsistensi dan keamanan dari data yang diakses.

2. Secara umum, _package_ `http` pada Flutter berfungsi untuk melakukan proses pengiriman dan pengambilan data dari API pada _web_ menggunakan _http request_. Selain pengambilan data, _package_ `http` juga berfungsi untuk melakukan _parsing_ pada data JSON sehingga data tersebut bisa diakses sebagai objek Dart.

3. Dalam proyek Flutter, `CookieRequest` digunakan untuk menghubungkan _cookie_ dan _session_ yang di-_generate_ server Django dengan proyek Flutter tersebut. Jika user melakukan login, maka server Django akan men-_generate_ cookie yang kemudian akan disimpan oleh CookieRequest pada server Flutter. Maka dari itu, CookieRequest juga harus didistribusi ke seluruh widget dalam proyek Flutter agar session yang dimiliki user tetap terjaga selama ia menjalankan aplikasi hingga logout. CookieRequest juga berfungsi untuk melakukan _passing_ _cookie_ jika suatu widget mengirimkan _http request_ ke server Django.

4. Pertama-tama, user menginput _field_ pada form untuk menspesifikasikan atribut untuk produk yang ingin dibuat. Setelah user melakukan _submit_, Flutter akan mengirimkan _POST request_ ke server Django, di mana request tersebut memuat data hasil _input_ user yang diubah dalam format JSON. Sesuai dengan _path_ yang dispesifikasikan dalam request, server Django kemudian akan memproses _request_ tersebut untuk men-_generate_  produk baru yang disimpan ke dalam _database_. Django kemudian akan mengirimkan response kembali ke server Flutter untuk memberikan indikasi bahwa penambahan produk baru berhasil dilakukan. Jika user menjalankan fitur untuk melihat semua daftar produk, server Flutter akan mengirimkan _GET request_ ke server Django, di mana Django akan mengirimkan kembali data JSON yang sudah memuat produk yang baru dibuat.

5. Pertama-tama, user menginput field pada laman register untuk membuat akun baru. Setelah user melakukan submit, FLutter akan mengirimkan data input tersebut dalam _http request_ ke server Django. Dalam Django, data input kemudian akan divalidasi dan jika valid, maka akan dibuat _user_ baru menggunakan `User.objects.create_user(username=username, password=password1)`. Django kemudian akan mengirimkan response yang mengindikasikan bahwa proses register berhasil. Ketika user melakukan login dengan menginput data akun yang sudah dibuat, Flutter akan mengirimkan request kembali ke Django dengan data input dari user. Dalam Django, dilakukan proses autentikasi terhadap data tersebut dan jika valid, maka Django akan melakukan proses login dan men-_generate_ _session_ baru menggunakan `auth_login(request, user)`. Response juga akan dikirimkan beserta dengan _cookie_ untuk mengindikasikan bahwa proses login berhasil. Setelah server Flutter menerima _response_ yang menunjukkan login berhasil, _cookie_ yang dikirim kemudian disimpan dan user akan dinavigasi dari halaman login ke halaman utama aplikasi. Selanjutnya, jika user mengklik tombol logout, Flutter akan mengirimkan _request_ ke server Django dengan path yang sesuai, di mana Django kemudian akan men-_terminate_ _session_ dari user dengan `auth_logout(request)`. Setelah server Flutter mengirimkan _response_ kembali dari Django, user akan dinavigasikan kembali ke halaman login.

6. Berikut merupakan prosedur _step-by-step_ saya untuk megimplementasikan checklist pada Tugas 9:

Pertama-tama, buatlah _app_ baru bernama `authentication` pada proyek Django sebelumnya. Setelah menambahkan app tersebut ke INSTALLED_APPS pada `settings.py`, lakukan instalasi _library_ `django-cors-headers` pada proyek Django tersebut. Tambahkan pula library tersebut pada requirements.txt dan settings.py (tambahkan pula _middleware_ `corsheaders.middleware.CorsMiddleware`). Tambahkan juga variabel-variabel berikut pada berkas `settings.py`:
```
CORS_ALLOW_ALL_ORIGINS = True
CORS_ALLOW_CREDENTIALS = True
CSRF_COOKIE_SECURE = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SAMESITE = 'None'
SESSION_COOKIE_SAMESITE = 'None'
```

Kemudian, bukalah berkas views.py direktori authentication dan buatlah method untuk mengautentikasi proses login dari aplikasi Flutter:
```
@csrf_exempt
def login(request):
    username = request.POST['username']
    password = request.POST['password']
    user = authenticate(username=username, password=password)
    if user is not None:
        if user.is_active:
            auth_login(request, user)
            # Status login sukses.
            return JsonResponse({
                "username": user.username,
                "status": True,
                "message": "Login sukses!"
                # Tambahkan data lainnya jika ingin mengirim data ke Flutter.
            }, status=200)
        else:
            return JsonResponse({
                "status": False,
                "message": "Login gagal, akun dinonaktifkan."
            }, status=401)
```

Tambahkan URL _path_ yang sesuai untuk method tersebut pada berkas `urls.py`. Selanjutnya, bukalah proyek Flutter dan tambahkan package `provider` dan `pbp_django_auth` (bisa dilakukan dengan _command_ `flutter pub add [NAMA_PACKAGE]`). Kemudian, bukalah berkas main.dart dan ubahlah isinya sehingga memuat objek Provider yang memberikan cookieRequest ke seluruh komponen aplikasi:
```
...

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return Provider(
      create: (_) {
        CookieRequest request = CookieRequest();
        return request;
      },
      child: MaterialApp(
        title: 'NetBuy',
        theme: ThemeData(
          colorScheme: ColorScheme.fromSwatch(
            primarySwatch: Colors.indigo,
          ).copyWith(secondary: Colors.indigo[300]),
          useMaterial3: true,
        ),
        home: const LoginPage(),
      ),
    );
  }
}
```

Setelah itu, buatlah berkas `login.dart` pada direktori `screens` dan tambahkan statuful widget `loginApp` yang berfungsi untuk mendefinisikan halaman login. Jangan lupa untuk menambahkan _logic_ untuk mengirimkan  _http request_ ke server Django untuk melakukan proses autentikasi login seperti berikut:
```
...
onPressed: () async {
    String username = _usernameController.text;
    String password = _passwordController.text;

    final response = await request
        .login("http://127.0.0.1:8000/auth/login/", {
    'username': username,
    'password': password,
    });
...
}
...
```
Pada berkas main.dart, ubahlah h`ome: const MyHomePage()` menjadi `home: const LoginPage()` sehingga halaman yang pertama ditampilkan adalah halaman login. Selanjutnya, dalam direktori authentication di proyek Django, buatlah method baru pada berkas `views.py` untuk melakukan autentikasi pada proses register seperti berikut:
```
@csrf_exempt
def register(request):
    if request.method == 'POST':
        data = json.loads(request.body)
        username = data['username']
        password1 = data['password1']
        password2 = data['password2']

        # Check if the passwords match
        if password1 != password2:
            return JsonResponse({
                "status": False,
                "message": "Passwords do not match."
            }, status=400)
        
        # Check if the username is already taken
        if User.objects.filter(username=username).exists():
            return JsonResponse({
                "status": False,
                "message": "Username already exists."
            }, status=400)
        
        # Create the new user
        user = User.objects.create_user(username=username, password=password1)
        user.save()
        
        return JsonResponse({
            "username": user.username,
            "status": 'success',
            "message": "User created successfully!"
        }, status=200)
    
    else:
        return JsonResponse({
            "status": False,
            "message": "Invalid request method."
        }, status=400)
    
@csrf_exempt
def logout(request):
    username = request.user.username

    try:
        auth_logout(request)
        return JsonResponse({
            "username": username,
            "status": True,
            "message": "Logout berhasil!"
        }, status=200)
    except:
        return JsonResponse({
        "status": False,
        "message": "Logout gagal."
        }, status=401)
```

Setelah menambahkan URL path yang sesuai pada berkas `urls.py`, bukalah kembali proyek Flutter dan buatlah berkas `register.dart` pada direktori `screens`. Pada berkas tersebut, buatlah stateful widget bernama `RegisterPage` yang mendefinisikan halaman yang memuat _form_ untuk registrasi akun. Seperti pada `login.dart` tambahkan pula logic untuk mengirimkan http request ke server Django untuk meng-_handle_ proses autentikasi seperti berikut:
```
...
final response = await request.postJson(
    "http://127.0.0.1:8000/auth/register/",
    jsonEncode({
    "username": username,
    "password1": password1,
    "password2": password2,
    }));
...
```

Selanjutnya, bukalah _endpoint_ JSON pada proyek Django sebelumnya dan _copy_ data JSON-nya. Selanjutnya, bukalah website QuickType dan salinlah data JSON tadi untuk men-_generate_ _class_ Model Flutter bernama `ShopEntry`. Kemudian, pada proyek Flutter, buatlah direktori `models/` pada subdirektori `lib/` dan tambahkan berkas `shop_entry.dart` pada direktori tersebut. Salinlah class `ShopEntry` dari QuickType tadi ke dalam berkas tersebut.

Selanjutnya, lakukan instalasi _package_ `http` pada proyek Flutter. Lalu, pada direktori `android/app/src/main/`, bukalah berkas `AndroidManifest.xml` dan tambahkan tag berikut setelah tag `<application>` untuk memperbolehkan akses internet pada proyek Flutter:
```
<uses-permission android:name="android.permission.INTERNET" />
```

Kemudian, buatlah berkas `list_shopentry.dart` pada direktori `lib/screens/` dan tambahkan stateful widget bernama `ShopEntryPage` untuk melakukan proses _fetching_ dari JSON dan menampilkan data (produk) dari server proyek Django. Tambahkan pula navigasi ke widget `ShopEntryPage` pada drawer aplikasi di berkas `left_drawer.dart`.

Selanjutnya, bukalah proyek Django dan pada berkas `main/views.py`, buatlah method untuk membuat _instance_ produk baru berdasarkan _request_ dari proyek Flutter seperti berikut:
```
@csrf_exempt
def create_shop_entry_flutter(request):
    if request.method == 'POST':

        data = json.loads(request.body)
        new_shop_entry = ShopEntry.objects.create(
            user=request.user,
            name=data["name"],
            price=int(data["price"]),
            description=data["description"]
        )

        new_shop_entry.save()

        return JsonResponse({"status": "success"}, status=200)
    else:
        return JsonResponse({"status": "error"}, status=401)
```

Selanjutnya, bukalah berkas `productentry_form.dart` pada proyek Flutter. Tambahkan _CookieRequest_ pada bagian atas method _Widget build_ dan ubahlah _logic_ untuk button _save_ sehingga ia akan mengirimkan request POST ke server proyek Django untuk membuat produk baru jika diklik:
```
...
onPressed: () async {
    if (_formKey.currentState!.validate()) {
    // Kirim ke Django dan tunggu respons
    final response = await request.postJson(
        "http://127.0.0.1:8000/create-flutter/",
        jsonEncode(<String, String>{
            'name': _name,
            'price': _price.toString(),
            'description': _description,
        }),
    );
    if (context.mounted) {
        if (response['status'] == 'success') {
            ScaffoldMessenger.of(context)
                .showSnackBar(const SnackBar(
            content: Text("Product added successfully!"),
            ));
            Navigator.pushReplacement(
                context,
                MaterialPageRoute(builder: (context) => MyHomePage()),
            );
        } else {
            ScaffoldMessenger.of(context)
                .showSnackBar(const SnackBar(
                content:
                    Text("Please try again."),
            ));
        }
    }
    }
},
...
```

Terakhir, untuk membuat fitur logout, buatlah method baru pada berkas `authentication/views.py` proyek Django untuk meng-handle proses logout dari aplikasi Flutter:
```
@csrf_exempt
def logout(request):
    username = request.user.username

    try:
        auth_logout(request)
        return JsonResponse({
            "username": username,
            "status": True,
            "message": "Logout berhasil!"
        }, status=200)
    except:
        return JsonResponse({
        "status": False,
        "message": "Logout gagal."
        }, status=401)
```

Kemudian, bukalah kembali proyek Flutter dan pada berkas `product_card.dart`, tambahkan `CookieRequest` pada bagian atas method `Widget build`. Ubahlah bagian onTap() pada widget InkWell menjadi `onTap: () async { ... }` serta tambahkan _logic_ untuk mengirimkan _request_ ke server proyek Django dan melakukan proses _logout_:
```
...
else if (item.name == "Logout") {
final response = await request.logout(
    "http://127.0.0.1:8000/auth/logout/");
String message = response["message"];
if (context.mounted) {
    if (response['status']) {
        String uname = response["username"];
        ScaffoldMessenger.of(context).showSnackBar(SnackBar(
            content: Text("$message See you soon, $uname."),
        ));
        Navigator.pushReplacement(
            context,
            MaterialPageRoute(builder: (context) => const LoginPage()),
        );
    } else {
        ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(
                content: Text(message),
            ),
        );
    }
}
}
...
```