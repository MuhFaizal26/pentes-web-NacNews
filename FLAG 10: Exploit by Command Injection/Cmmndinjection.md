# FLAG [10] 
### Exploit by Command Injection

### Description 
Apa kode akses vault yang ditemukan di dalam file /opt/nac/final_directive.txt setelah mengakses halaman diagnostik admin?

### Hint
💡 Hint Flag 10: Command Injection, Url

### Analysis
Langkah awal sesuai dengan salah satu hint yang diberikan yaitu Url kami mencoba mengidentifikasi url page administrasi yaitu http://160.25.222.15:8070/admin dengan pola Url yang berhubungan dengan soal. Ditemukan lah /admin/diagnostics.php? yang menuju ke error code 403 dengan keterangan Usage: diagnostics.php?token=YOUR_TOKEN yang berarti kita memerlukan token spesial untuk mengakses halaman diagnostik tersebut.

### Solution
1. Akses Halaman diagnostik
   
Karena akses ke halaman diagnostik memerlukan token, kita kembali ke /var/log/nac/admin_actions.log pada flag sebelumnya yang berisi log aktifitas admin serta token yang kita butuhkan untuk mengakses halaman diagnostik yaitu (7f3a9b2c). Maka didapatlah Url http://160.25.222.15:8070/admin/diagnostics.php?token=7f3a9b2c sebagai url untuk mengakses halaman diagnostik.

<img width="1371" height="720" alt="Screenshot 2026-04-09 225138" src="https://github.com/user-attachments/assets/d130bcd3-4319-4fa8-8ac3-4e51f84d06f7" />


2. Identifikasi Kerentanan Command Injection
   
Setelah masuk ke panel admin, kami mengakses menu Diagnostics. Fitur ini menyediakan alat pengujian jaringan (Ping) yang menerima input dari pengguna. Melalui pengujian input, ditemukan bahwa sistem tidak melakukan sanitasi terhadap karakter khusus. Kami memanfaatkan celah OS Command Injection dengan menggunakan karakter pemisah perintah ; (semicolon) untuk menjalankan perintah sistem operasi Linux di luar fungsi aslinya.

<img width="1359" height="669" alt="Screenshot 2026-04-06 203556" src="https://github.com/user-attachments/assets/91db2ce2-fab7-4a41-b499-3efb4e224bb6" />


3. Eksplorasi Direktori dan Pengambilan Data
   
Kami mencoba membaca file /opt/nac/final_directive.txt menggunakan perintah cat, namun file tersebut hanya berisi dokumen arsip. Untuk mencari data yang lebih relevan, kami melakukan inspeksi direktori lebih dalam menggunakan perintah ls -la /opt/nac/.

<img width="1357" height="725" alt="Screenshot 2026-04-06 204143" src="https://github.com/user-attachments/assets/69a4d357-0380-4465-98ca-2496dfca5bfe" />


<img width="1322" height="659" alt="Screenshot 2026-04-06 205015" src="https://github.com/user-attachments/assets/5bb0c77b-f190-477b-a03f-7b5942b07e9e" />

4. Pengambilan Kode Akses (Flag)
   
Hasil inspeksi menunjukkan adanya symbolic link tersembunyi bernama .runtime_cache yang mengarah ke file asli di /opt/nac/vault/.flag_store. Kami mengeksekusi perintah cat /opt/nac/vault/.flag_store melalui celah injeksi tersebut. Langkah ini berhasil menampilkan isi file yang berisi kode flag yang di cari (d3c0d3_th3_v4ult_th1s)

<img width="1320" height="681" alt="Screenshot 2026-04-06 205226" src="https://github.com/user-attachments/assets/a8c14523-9072-4044-8b05-d8a5e50d9b29" />

5. Submit Flag Submit flag yang sudah ditemukan (d3c0d3_th3_v4ult_th1s) ke bot.
   
![WhatsApp Image 2026-04-05 at 10 16 43](https://github.com/user-attachments/assets/a3df403a-00cc-4aaa-9f8b-755f5c7ef4f4)

### 🛡️ Vulnerability Assessment
* **Vulnerability:** OS Command Injection (Improper Neutralization of Special Elements used in an OS Command)
* **Severity:** High
* **CVSS v4.0 Score:** **8.7 (High)**
* **CVSS Vector:** `CVSS:4.0/AV:N/AC:L/AT:N/PR:L/UI:N/VC:H/VI:H/VA:H/SC:N/SI:N/SA:N`
> *Catatan: Privileges Required (PR) diset ke Low (L) karena eksploitasi membutuhkan token diagnostik tingkat rendah untuk mengakses antarmuka injeksi.*

<img width="1161" height="509" alt="image" src="https://github.com/user-attachments/assets/a3dd271d-8bff-48ae-a886-1a7d74633363" />

### 💡 Saran Rekomendasi Mitigasi
1. **Hindari Pemanggilan System Shell:** Jangan pernah menggunakan fungsi PHP seperti `shell_exec()`, `exec()`, `passthru()`, atau `system()` jika memungkinkan. Gunakan *library* PHP bawaan atau API jaringan terisolasi untuk fitur diagnostik.
2. **Validasi Input Whitelist (Strict Input Validation):** Jika eksekusi perintah OS tidak dapat dihindari, batasi input murni menggunakan validasi *whitelist*. Contoh: gunakan `filter_var($ip, FILTER_VALIDATE_IP)` untuk memastikan *input* murni berupa alamat IP tanpa karakter spesial.
3. **Gunakan Escape Functions:** Selalu bungkus variabel *input* pengguna menggunakan fungsi `escapeshellarg()` dan `escapeshellcmd()` sebelum menggabungkannya ke dalam *string* perintah OS.
4. **Terapkan Prinsip Least Privilege:** Konfigurasikan layanan web (Apache/Nginx/PHP-FPM) agar berjalan dengan hak akses *user* sistem yang terbatas (misal: `www-data`), sehingga mencegah peretas mengakses direktori sensitif di luar *web root* seperti `/opt/` atau `/var/`.
