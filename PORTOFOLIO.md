# PORTOFOLIO
# PROYEK AKHIR SISTEM OPERASI

## Sistem Antrean Pekerjaan dan Monitoring Proses Linux

---

## 1. Identitas Mahasiswa

| Keterangan | Identitas |
|---|---|
| Nama | Muhammad Hadziq |
| NIM | 2510512031 |
| Program Studi | Sistem Informasi |
| Mata Kuliah | Sistem Operasi |
| Kelompok | 4 |
| Pilihan Proyek | 5B |
| Posisi | Anggota 1 (Ketua Kelompok) |
| Tanggung Jawab | Pengajuan Pekerjaan dan Koordinasi Repository |

## 2. Deskripsi Proyek

Proyek akhir Sistem Operasi ini berjudul **Sistem Antrean Pekerjaan dan Monitoring Proses Linux**.

Proyek ini bertujuan membuat sistem pengelolaan pekerjaan komputasi sederhana menggunakan Bash Script pada sistem operasi Linux.

Sistem menggunakan metode FIFO (First In First Out), yaitu pekerjaan yang masuk lebih dahulu akan diproses lebih dahulu oleh worker.

Fitur utama sistem meliputi:
- Pengajuan pekerjaan dengan parameter tervalidasi.
- Pengelolaan antrean pekerjaan FIFO.
- Penguncian worker agar tidak terjadi pemrosesan ganda.
- Pencatatan status pekerjaan, PID, dan exit status.
- Pembatasan waktu eksekusi pekerjaan (timeout).
- Penghentian proses menggunakan SIGTERM dan SIGKILL.
- Monitoring CPU dan memori.
- Pembatasan akses menggunakan permission dan group Linux.
- Pembuatan laporan dan integrasi Docker dengan bind mount read-only.

Proyek dikerjakan secara berkelompok dengan pembagian tanggung jawab masing-masing anggota menggunakan Git dan GitHub.

## 3. Repository dan Pengembangan

### Repository utama kelompok

https://github.com/h44dziq/job-queue-kelompok-4

Repository utama berada pada akun GitHub saya dan digunakan sebagai sumber integrasi seluruh pekerjaan anggota kelompok.

### Repository portofolio pribadi

https://github.com/h44dziq/portofolio-sistem-operasi

Repository ini adalah repository baru yang khusus dibuat untuk dokumentasi dan portofolio pribadi. Repository ini tidak memuat salinan kode kelompok. Isinya berfokus pada laporan kontribusi, penjelasan implementasi yang saya kerjakan, dan hasil pengujian.

GitHub tidak mengizinkan fork terhadap repository milik sendiri. Karena repository utama kelompok berada pada akun saya, portofolio pribadi disimpan di repository terpisah.

### Branch yang digunakan

Saya menggunakan dua branch untuk pengerjaan bagian pengajuan pekerjaan dan dokumentasi.

**1. Branch fitur pengajuan pekerjaan**

`feat/muhammadhadziq-submitjob`

https://github.com/h44dziq/job-queue-kelompok-4/tree/feat/muhammadhadziq-submitjob

Branch ini digunakan untuk mengembangkan `submit.sh`, konfigurasi daftar job yang diizinkan, dan script job dummy.

**2. Branch dokumentasi dan bukti pengujian**

`docs/tests-evidence`

https://github.com/h44dziq/job-queue-kelompok-4/tree/docs/tests-evidence

Branch ini digunakan untuk menyusun rancangan sistem, matriks pengujian, README, dan evidence hasil pengujian dari mesin virtual.

### Pull request

| Pull Request | Isi |
|---|---|
| #1 | `submit.sh`, `config/allowed_jobs.txt`, dan script job dummy |
| #4 | Validasi rentang parameter dan penolakan akses pada `submit.sh` |
| #10 | Dokumentasi, rancangan, matriks uji, dan evidence |

Sebagai ketua kelompok, saya juga meninjau dan menggabungkan pull request anggota lain, yaitu #3 (worker FIFO dan lock), #7 (timeout dan penghentian), #8 (status dan exit status), serta #9 (monitoring, akses, laporan, dan Docker).

## 4. Tanggung Jawab dan Implementasi

Sebagai Anggota 1 sekaligus ketua kelompok, saya bertanggung jawab mengembangkan fitur pengajuan pekerjaan, menyusun konfigurasi job, dan mengoordinasikan integrasi seluruh komponen sistem.

File utama yang dikerjakan:

`scripts/submit.sh`

File pendukung:

- `config/allowed_jobs.txt`
- `scripts/jobs/job_ringan.sh`
- `scripts/jobs/job_gagal.sh`
- `scripts/jobs/job_lama.sh`
- `README.md`
- `docs/rancangan.md`
- `tests/README.md`
- `evidence/`

Implementasi yang dilakukan:

1. Membuat Bash Script `submit.sh` untuk menerima nama job dan parameter dari pengguna.
2. Memeriksa jumlah argumen agar sesuai format `submit.sh <nama_job> <parameter>`.
3. Memeriksa hak akses pengguna terhadap folder `pending/` sebelum job diajukan.
4. Membaca daftar job yang diizinkan dari `config/allowed_jobs.txt`.
5. Menolak job yang tidak ada di whitelist atau tidak memiliki script pelaksana.
6. Memvalidasi parameter agar hanya berupa angka 1 sampai 6 digit.
7. Memvalidasi parameter agar berada dalam rentang yang ditentukan untuk setiap job.
8. Membuat ID job dari timestamp presisi nanodetik dan nama job.
9. Menulis file job ke file sementara, lalu memindahkannya dengan `mv` agar file terbaca utuh oleh worker.
10. Mencatat setiap pengajuan dan penolakan ke log bersama `/var/lib/job-queue/queue.log`.
11. Menyusun tiga job dummy dengan karakteristik berbeda untuk pengujian sistem.
12. Menyusun rancangan sistem, matriks pengujian, dan evidence hasil pengujian.

### Daftar job yang diizinkan

| Nama Job | Rentang Parameter | Karakteristik |
|---|---|---|
| `job_ringan` | 1–30 | Perulangan CPU, selesai sesuai durasi |
| `job_gagal` | 1–100 | Langsung keluar dengan exit code tertentu |
| `job_lama` | 1–120 | Menunggu menggunakan `sleep`, dipakai untuk uji timeout |

### Mekanisme validasi `submit.sh`

| Pemeriksaan | Perilaku jika gagal | Exit |
|---|---|---|
| Jumlah argumen harus 2 | Menampilkan cara pakai | 2 |
| Folder `pending/` harus dapat ditulis | Menolak pengajuan | 5 |
| File whitelist harus tersedia | Menampilkan galat | 1 |
| Nama job harus terdaftar dan script tersedia | Menolak job | 3 |
| Parameter harus berupa angka | Menolak parameter | 4 |
| Parameter harus dalam rentang job | Menolak parameter | 4 |
| Penulisan file job berhasil | Menampilkan galat | 1 |
| Pengajuan berhasil | Job masuk `pending/` | 0 |

### Konfigurasi pengajuan

| Konfigurasi | Nilai |
|---|---|
| Format argumen | `submit.sh <nama_job> <parameter>` |
| Format parameter | Angka 1–6 digit |
| Panjang ID job | Timestamp `YYYYMMDDHHMMSS` + nanodetik + nama job |
| Lokasi antrean | `/var/lib/job-queue/pending/` |
| Log bersama | `/var/lib/job-queue/queue.log` |
| Umask | `007` |
| Penulisan file | File sementara lalu `mv` (atomik) |

## 5. Pengujian Fitur Pengajuan Pekerjaan

Pengujian dilakukan dalam dua bentuk, yaitu pengujian positif dengan job yang valid dan pengujian negatif dengan job yang tidak diizinkan. Pengujian dijalankan pada Ubuntu Server 22.04 melalui VirtualBox.

### A. Pengujian Positif — Job Valid Diterima

**Tujuan:**

Memastikan job yang terdaftar di whitelist dan parameternya valid dapat masuk ke antrean.

Perintah pengajuan pekerjaan:

```bash
sudo -u pengguna1 /opt/job-queue/scripts/submit.sh job_ringan 5
```

Hasil:

```text
Job diterima: 20261110023105152928283_job_ringan (parameter 5)
exit=0
```

Pemeriksaan isi antrean:

```bash
ls /var/lib/job-queue/pending
```

Hasil:

```text
20261110023105152928283_job_ringan.job
exit=0
```

Potongan log:

```text
2026-11-10 02:31:05 | PID=2415 | pengguna1 | job_ringan | menunggu (20261110023105152928283_job_ringan)
```

Informasi proses:

- Pengguna: `pengguna1`
- PID proses `submit.sh`: 2415
- Exit status: 0
- Status: job masuk ke folder `pending/`.

**Kesimpulan:** Job valid berhasil diterima, dicatat pada log, dan file job tersedia di antrean.

### B. Pengujian Negatif — Job Tidak Diizinkan Ditolak

**Tujuan:**

Memastikan job yang tidak terdaftar di whitelist ditolak sebelum menyentuh antrean.

Perintah pengajuan pekerjaan:

```bash
sudo -u pengguna1 /opt/job-queue/scripts/submit.sh rm 5
```

Hasil:

```text
Ditolak: job 'rm' tidak diizinkan
exit=3
```

Pemeriksaan isi antrean:

```bash
ls /var/lib/job-queue/pending
```

Hasil:

```text
20261110023105152928283_job_ringan.job
exit=0
```

Antrean tidak bertambah karena file job baru tidak dibuat.

Potongan log:

```text
2026-11-10 02:31:05 | PID=2426 | pengguna1 | rm | ditolak (job 'rm' tidak diizinkan)
```

Informasi proses:

- Pengguna: `pengguna1`
- PID proses `submit.sh`: 2426
- Exit status: 3
- Status: ditolak.

**Kesimpulan:** Job yang tidak diizinkan berhasil ditolak. Pengguna tidak dapat menjalankan perintah sembarang melalui sistem antrean.

### C. Pengujian Integrasi sebagai Ketua Kelompok

Selain menguji fitur pengajuan, saya memverifikasi hasil pengujian seluruh anggota dan memastikan setiap komponen saling terhubung.

| ID | Penanggung Jawab | Hasil yang Diverifikasi | Status | Bukti |
|---|---|---|---|---|
| U2 | Arvin Maulana | Dua job berjalan sesuai urutan FIFO; worker kedua ditolak dengan exit 6 | Lulus | `evidence/U2-fifo-lock.txt` |
| U3 | Radhitya | `job_ringan` tercatat BERHASIL (exit 0); `job_gagal` tercatat GAGAL (exit 3) | Lulus | `evidence/U3-status-exit.txt` |
| U4 | Erlang Yudantara | `job_lama 3` selesai; `job_lama 20` dihentikan SIGTERM dengan exit 124 | Lulus | `evidence/U4-timeout.txt` |
| U5 | Andika | Container membaca laporan; penulisan ke mount read-only ditolak | Lulus | `evidence/U5-akses-laporan-docker.txt` |
| Tambahan | Andika | `tamu1` ditolak saat mengajukan job (exit 5); monitoring CPU dan memori berjalan | Lulus | `evidence/U5-akses-laporan-docker.txt`, `evidence/monitor.txt` |

**Kesimpulan:** Seluruh komponen sistem berhasil diintegrasikan. Job yang diajukan melalui `submit.sh` dapat diproses worker, dicatat status dan exit status-nya, dibatasi waktu eksekusinya, serta dilaporkan melalui container Docker.

## 6. Ringkasan Hasil Pengujian

| No. | Skenario | Hasil | Exit Status |
|---|---|---|---|
| 1 | Job valid `job_ringan 5` | Diterima | 0 |
| 2 | Job tidak diizinkan `rm 5` | Ditolak | 3 |
| 3 | Job melewati timeout | GAGAL | 124 |
| 4 | Job mengabaikan SIGTERM | TERMINATED | 137 |

Pengujian pengajuan pekerjaan menunjukkan bahwa validasi whitelist dan parameter berjalan sesuai rancangan.

## 7. Analisis Teori Sistem Operasi

### A. Proses dan PID

Setiap kali `submit.sh` dijalankan, Linux membuat proses baru dengan PID unik. PID tersebut dicatat pada `queue.log` sehingga setiap pengajuan dapat ditelusuri.

Perintah `sudo -u pengguna1` menjalankan script sebagai pengguna lain. Sistem operasi memeriksa hak akses pengguna tersebut sebelum proses dijalankan.

### B. Antrean FIFO

Antrean diimplementasikan sebagai kumpulan file di folder `pending/`. Nama file diawali timestamp sehingga pengurutan nama file sama dengan urutan kedatangan job. Worker mengambil file paling awal, sehingga berlaku FIFO.

Ini merupakan antrean tingkat aplikasi, bukan penjadwalan proses oleh kernel.

### C. Atomisitas Penulisan File

`submit.sh` menulis data job ke file sementara `.<ID_JOB>.tmp`, kemudian memindahkannya menjadi `<ID_JOB>.job` menggunakan `mv`.

Operasi `mv` pada filesystem yang sama bersifat atomik. Worker tidak akan pernah membaca file job yang belum selesai ditulis. Mekanisme ini mencegah race condition antara proses pengajuan dan proses worker.

### D. Permission, Group, dan Umask

Folder antrean dimiliki `root:jobqueue` dengan mode `2770`. Bit setgid membuat file baru di dalamnya mengikuti group `jobqueue`.

`umask 007` pada `submit.sh` memastikan file job tidak dapat dibaca pengguna di luar group. Hak akses diperiksa oleh kernel, bukan oleh script.

Pengguna `tamu1` yang bukan anggota group `jobqueue` tidak dapat mengakses folder antrean dan ditolak dengan exit 5.

### E. Whitelist dan Prinsip Hak Akses Minimum

Nama job tidak langsung dieksekusi sebagai perintah. `submit.sh` hanya menerima nama job yang terdaftar di `config/allowed_jobs.txt` dan memiliki script di `scripts/jobs/`.

Dengan demikian pengguna tidak dapat menjalankan perintah arbitrer seperti `rm`. Ini menerapkan prinsip hak akses minimum, yaitu pengguna hanya dapat melakukan tindakan yang memang diizinkan administrator.

### F. Validasi Parameter

Parameter diperiksa menggunakan ekspresi reguler `^[0-9]{1,6}$`, lalu dibandingkan dengan rentang setiap job.

Validasi dilakukan sebelum file job dibuat, sehingga data yang tidak valid tidak pernah masuk ke antrean.

### G. Exit Status

Exit status menunjukkan hasil akhir eksekusi proses.

Dalam proyek ini:
- 0 menunjukkan pengajuan berhasil.
- 1 menunjukkan galat umum.
- 2 menunjukkan cara pakai salah.
- 3 menunjukkan job tidak diizinkan.
- 4 menunjukkan parameter tidak valid.
- 5 menunjukkan akses ditolak.

## 8. Aktivitas Pengerjaan Proyek

Selain mengembangkan fitur pengajuan pekerjaan, saya melakukan beberapa aktivitas sebagai ketua kelompok.

### A. Persiapan Lingkungan Linux

- Menggunakan Ubuntu Server 22.04 LTS melalui VirtualBox.
- Menyiapkan mesin virtual dengan 3 CPU, RAM 2 GB, dan partisi root 11 GB.
- Memeriksa versi kernel, Bash, dan Docker.
- Memeriksa struktur direktori proyek.
- Memeriksa dan mengubah permission script menggunakan `chmod`.
- Memeriksa sintaks Bash menggunakan `bash -n`.
- Menyiapkan akun `operator1`, `pengguna1`, dan `tamu1` beserta group `jobqueue` dan `jobqueueop`.

### B. Pengelolaan Git dan GitHub

Aktivitas Git yang dilakukan:

1. Membuat repository utama kelompok di GitHub.
2. Melakukan clone repository pada mesin virtual.
3. Membuat branch `feat/muhammadhadziq-submitjob` untuk fitur pengajuan.
4. Melakukan perubahan pada `submit.sh`, `config/allowed_jobs.txt`, dan `scripts/jobs/`.
5. Melakukan commit perubahan.
6. Melakukan push ke repository GitHub.
7. Membuat pull request #1 dan #4.
8. Meninjau dan menggabungkan pull request anggota lain (#3, #7, #8, #9).
9. Membuat branch `docs/tests-evidence` untuk dokumentasi dan evidence.
10. Membuat pull request #10.
11. Memeriksa riwayat commit menggunakan `git log`.
12. Melakukan merge pembaruan dari `origin/main` ke branch pekerjaan.
13. Menyusun README dan rancangan sistem sebagai acuan seluruh anggota.

### C. Dokumentasi dan Pengujian

- Menyusun `docs/rancangan.md` yang memuat alur job, struktur direktori, matriks akses, dan alasan rancangan.
- Menyusun `tests/README.md` yang memuat matriks skenario U1–U5.
- Mengumpulkan evidence hasil pengujian dari seluruh anggota.
- Memeriksa output eksekusi pekerjaan.
- Memeriksa PID proses.
- Memeriksa exit status.
- Memeriksa pencatatan log bersama.
- Memverifikasi konsistensi hasil pengujian dengan rancangan sistem.

## 9. Bukti Dokumentasi

Bukti lengkap tersimpan di repository utama kelompok pada folder `evidence/`. Ringkasan bukti yang dirujuk pada portofolio ini:

| No. | Bukti | Keterangan |
|---|---|---|
| 1 | `evidence/U1-pengajuan.txt` | Pengujian positif dan negatif pengajuan job |
| 2 | `evidence/lingkungan.txt` | Lingkungan pengerjaan Ubuntu Server 22.04 |
| 3 | `docs/rancangan.md` | Rancangan sistem, alur job, dan matriks akses |
| 4 | `tests/README.md` | Matriks pengujian seluruh anggota |
| 5 | `README.md` | Dokumentasi instalasi, penggunaan, dan kontribusi |
| 6 | `evidence/U2-fifo-lock.txt` | Bukti FIFO dan lock worker |
| 7 | `evidence/U3-status-exit.txt` | Bukti status dan exit status |
| 8 | `evidence/U4-timeout.txt` | Bukti timeout dan penghentian proses |
| 9 | `evidence/U5-akses-laporan-docker.txt` | Bukti akses, laporan, dan Docker |
| 10 | `evidence/monitor.txt` | Bukti monitoring CPU dan memori |
| 11 | Riwayat commit dan pull request | Bukti kontribusi melalui Git dan GitHub |

## 10. Kesimpulan dan Pembelajaran

Melalui proyek akhir Sistem Operasi ini, saya memperoleh pengalaman dalam mengembangkan sistem antrean pekerjaan menggunakan Bash Script pada Linux.

Fokus utama pekerjaan saya adalah implementasi pengajuan pekerjaan yang aman melalui `submit.sh`, meliputi validasi jumlah argumen, whitelist job, validasi parameter, pemeriksaan hak akses, penulisan file secara atomik, dan pencatatan log.

Saya memahami bagaimana proses dijalankan, bagaimana hak akses diperiksa oleh kernel, bagaimana file dapat digunakan sebagai antrean, serta bagaimana penulisan atomik mencegah race condition antara pengaju dan worker.

Sebagai ketua kelompok, saya juga memperoleh pengalaman mengoordinasikan pekerjaan lintas anggota menggunakan Git dan GitHub, mulai dari pembuatan repository, branch, commit, push, pull request, review, merge, hingga penyusunan dokumentasi dan evidence.

Proyek ini membantu saya memahami penerapan teori sistem operasi secara langsung, khususnya manajemen proses, PID, exit status, permission, group, umask, antrean FIFO, dan sinkronisasi sederhana antarproses.

---

**Dokumentasi ini disusun sebagai portofolio kontribusi pribadi dalam Proyek Akhir Sistem Operasi Kelompok 4.**
