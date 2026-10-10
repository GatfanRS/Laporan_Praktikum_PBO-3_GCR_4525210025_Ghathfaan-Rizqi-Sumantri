# Studi Kasus: RekeningBank (Sesi 3 — Constructor Berdelegasi, Anggota Statis, & Konstanta)

Proyek latihan pemrograman berorientasi objek (OOP) ini berfokus pada penerapan konsep **konstanta kelas**, **anggota statis (`static`)**, **delegasi constructor (`this(...)`)**, serta **named constructor** (khusus PHP) untuk menjaga integritas data melalui *invariant* yang ketat.

---

## 📌 Aturan Invariant
1. **Saldo tidak pernah negatif**: Setiap operasi pengurangan saldo (seperti penarikan atau potongan biaya administrasi) harus divalidasi agar saldo tidak kurang dari 0.
2. **Nomor rekening tidak berubah**: Properti nomor rekening bersifat *immutable* (konstan atau *read-only* setelah objek diinisialisasi).
3. **Setoran dan penarikan bernilai positif**: Argumen bernilai nol atau negatif harus ditolak dengan memicu *exception*.

---

## 🛠️ Ringkasan Fitur Kelas `RekeningBank`

### 1. Konstanta Kelas
Memusatkan nilai konfigurasi menggunakan anggota statis konstan agar tidak ada angka literal (*magic numbers*) di dalam method:
* **Bunga Tahunan**: `0.025` (2.5%)
* **Biaya Administrasi**: `Rp5.000,00`
* **Batas Penarikan Sekali Transaksi**: `Rp5.000.000,00`

### 2. Anggota & Method Statis (`static`)
* **Penghitung Rekening (`jumlahRekening`)**: Menyimpan total objek rekening yang berhasil dibuat secara global.
* **`getJumlahRekening()`**: Mengembalikan jumlah total rekening yang pernah diinisialisasi.
* **`bungaSetahun(double/float pokok)`**: Method utilitas statis untuk menghitung estimasi bunga dalam setahun berdasarkan jumlah saldo/pokok tanpa bergantung pada *state* instance tertentu.

### 3. Delegasi Constructor (Java) & Default Parameter / Named Constructor (PHP)
* **Java**: Menggunakan constructor ringkas yang mendelegasikan tugas inisialisasi ke constructor utama via `this(...)` untuk menghindari duplikasi validasi dan kesalahan perhitungan penghitung objek.
* **PHP**: Memanfaatkan *default parameter* pada constructor utama serta *named constructor* statis (`rekeningPelajar()`) dengan `new static()` untuk mendukung *late static binding*.

---

## 🚀 Contoh Penggunaan (Java Main)

```java
public class Main {
    public static void main(String[] args) {
        // Membuat objek rekening dengan berbagai constructor
        RekeningBank a = new RekeningBank("111", "Ani", 1_000_000);
        RekeningBank b = new RekeningBank("222", "Budi"); // Constructor ringkas (saldo 0)
        RekeningBank c = new RekeningBank("333", "Citra", 250_000);

        System.out.println("Jumlah rekening: " + RekeningBank.getJumlahRekening()); // Output: 3

        // Operasi Setor & Tarik
        a.setor(500_000);
        
        try {
            a.tarik(9_999_999); // Ditolak karena melebihi batas / saldo
        } catch (RuntimeException e) {
            System.out.println("Ditolak: " + e.getMessage());
        }

        // Potong Biaya Admin (Aman dari saldo negatif)
        b.potongBiayaAdmin(); 

        // Hitung Bunga Statis
        double bungaAni = RekeningBank.bungaSetahun(a.getSaldo());
    }
}