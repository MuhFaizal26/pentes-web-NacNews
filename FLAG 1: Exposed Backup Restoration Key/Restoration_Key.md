# FLAG [1]: Exposed Backup Restoration Key

### Description
Apa restoration key yang tersimpan di dalam file backup lama pada website ini?

### Hint
💡 Hint: Common File, Encode

### Analysis
Kami memulai penetrasi dengan melakukan enumerasi direktori menggunakan `gobuster` untuk memetakan *path* yang ada di dalam server target. Hasilnya menunjukkan adanya file `robots.txt`. Dari inspeksi file tersebut, kami menemukan direktori *backup* tersembunyi dan sebuah *string* Base64. Setelah di-*decode*, *string* tersebut merujuk pada nama file *backup*. Kami kemudian mengunduh file tersebut dan membaca isinya untuk mendapatkan kunci restorasi.

### Solution
**1. Scanning Direktori (Gobuster)**
Kami menjalankan *tools* `gobuster` di terminal untuk mencari file dan direktori tersembunyi menggunakan *wordlist* `common.txt` dengan tambahan ekstensi spesifik:
`gobuster dir -u http://160.25.222.15:8070 -w /usr/share/wordlists/dirb/common.txt -x bak,backup,old,zip,txt,php.bak`

