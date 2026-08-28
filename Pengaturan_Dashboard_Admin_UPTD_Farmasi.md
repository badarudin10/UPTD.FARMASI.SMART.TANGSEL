# Pengaturan Dashboard Admin UPTD Farmasi

Dashboard admin tersedia pada alamat:

`https://badarudin10.github.io/UPTD.FARMASI.SMART.TANGSEL/admin.html`

## Membuat akun admin

Buka Firebase Console untuk project `digital-books-d22c9`, masuk ke **Authentication**, pilih **Sign-in method**, aktifkan **Email/Password**, kemudian buka tab **Users** dan buat akun admin baru. Gunakan email dan kata sandi tersebut pada halaman `admin.html`.

## Fitur dashboard

Dashboard memantau koleksi `tamu` secara realtime, mengubah status menjadi `Menunggu`, `Dalam Proses`, `Selesai`, atau `Dilewati`, mencatat petugas, mencatat waktu proses dan selesai, mengisi item obat, serta mengunduh SBBK setelah pelayanan selesai. Dashboard juga memantau koleksi `Konsultasi`, menyimpan daftar petugas pada `admin_petugas`, dan menyimpan katalog obat pada `admin_obat`.

## Aturan keamanan Firestore

Login pada halaman admin adalah lapisan akses antarmuka. Untuk keamanan data yang sebenarnya, terapkan Firebase Security Rules agar operasi perubahan hanya dapat dilakukan oleh pengguna yang sudah login. Contoh awal berikut harus disesuaikan dengan kebutuhan organisasi sebelum dipakai di produksi:

```text
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /tamu/{id} {
      allow create: if true;
      allow read: if true;
      allow update, delete: if request.auth != null;
    }
    match /Konsultasi/{id} {
      allow create: if true;
      allow read, update, delete: if request.auth != null;
    }
    match /admin_petugas/{id} {
      allow read, write: if request.auth != null;
    }
    match /admin_obat/{id} {
      allow read, write: if request.auth != null;
    }
  }
}
```

Aturan tersebut dibuat agar tamu tetap dapat membuat registrasi dan memantau status, sedangkan perubahan data pelayanan, data konsultasi, daftar petugas, dan katalog obat memerlukan login admin. Untuk keamanan tingkat lanjut, tambahkan klaim peran admin melalui Firebase Admin SDK sehingga tidak semua pengguna terautentikasi dapat mengubah data.

## Catatan geolokasi

Registrasi tamu hanya diterima jika browser memberikan koordinat dan jarak perangkat tidak lebih dari 100 meter dari koordinat UPTD yang sudah ada di aplikasi. Browser harus menggunakan HTTPS dan pengguna harus memberikan izin lokasi.

## Template SBBK

Template SBBK dibuat langsung saat tombol **Unduh SBBK** ditekan dari dashboard admin. Dokumen memuat nomor antrean, tanggal, penerima, instansi, keperluan, rincian item, petugas, durasi pelayanan, keterangan, dan area tanda tangan.
