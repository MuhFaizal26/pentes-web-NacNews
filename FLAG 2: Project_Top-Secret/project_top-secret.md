# FLAG [2]: Top-Secret Project Codename

### Description
Apa codename proyek top-secret yang tercantum di dalam memo internal pada database?

### Hint
💡 Hint: Sql Injection

### Analysis
Setelah berhasil masuk ke web NacNews menggunakan kredensial *user* standar (Alex Reader), kami mulai memetakan semua titik *input* yang memungkinkan interaksi dengan *backend*. Perhatian kami langsung tertuju pada fitur pencarian artikel yang menggunakan *endpoint* `search.php?q=`. 

Kami berasumsi bahwa *input* dari parameter `q` ini diteruskan langsung ke dalam eksekusi *query* SQL database. Lewat beberapa tes manual (*fuzzing*), aplikasi terlihat menelan *input* kami mentah-mentah tanpa adanya mekanisme pembersihan (sanitasi) yang memadai. Karena celah ini bersifat *blind* (error database tidak dimunculkan di layar), kami memutuskan untuk mengotomatisasi serangan menggunakan **SQLMap**. Tantangannya, kami harus menjaga sesi agar tetap "login" selama SQLMap bekerja, jika tidak, server akan terus memblokir *request* kami.

### Solution
