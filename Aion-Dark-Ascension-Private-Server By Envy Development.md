# 🛡️ Aion: Dark Ascension — Full Stack Private Server Platform
### Portfolio Project — Juan Garcia

---

## 📌 Overview

**Aion: Dark Ascension** is a full-stack web platform and desktop launcher built for a custom **Aion Online 3.5 private server**. Proyek ini dikerjakan sepenuhnya dari **nol (0)** hingga tahap *production-ready*. 

Tidak ada template atau boilerplate instan yang digunakan. Semua arsitektur, mulai dari routing Next.js, sistem autentikasi, launcher Electron, integrasi kriptografi TPM tingkat hardware, injeksi DLL C++ (anti-cheat), modifikasi source code Java server, manajemen database (Game dan Web), sistem in-game shop, gacha, redeem code, portal berita, dukungan laporan pemain, hingga integrasi file patch game dilakukan secara manual baris demi baris.

> Built with: **Next.js 15 (App Router)**, **TypeScript**, **Electron**, **React**, **TailwindCSS**, **C++ (DLL)**, **Java (Server Mods)**, **MySQL**, **PowerShell**, **Node.js**

---

## 🧰 Tech Stack Terlengkap

| Layer | Technology |
|---|---|
| Frontend Framework | Next.js 15 (App Router) |
| UI | React 19, TailwindCSS v4, Lucide Icons, Glassmorphism CSS |
| Backend (API Routes) | Next.js API Routes (TypeScript), Node.js Automation Scripts |
| Database | MySQL (via XAMPP, Cross-database join `z_zaion` & `z_zwebaion`), mysql2/promise |
| Desktop Launcher | Electron 44, Vite, React |
| Build Tool | electron-builder (NSIS installer dengan auto-update) |
| Hardware Security (Anti-Cheat) | Windows CNG / TPM 2.0, RSA-XML, SHA-256, Challenge-Response |
| Client Security (Anti-Cheat) | C++ (EnvyGuard.dll) - Memory scanning & process monitoring |
| Server-Side Security (Game) | Java (AL-Login modifikasi source code) |
| Email & Notifications | Nodemailer (SMTP) |
| Auth & Security | SHA-1 password hashing (untuk kompabilitas Java Server), JWT-like session tokens |

---

## 🌐 Web Portal (Next.js)

Website utama yang melayani pemain dengan fitur lengkap dan integrasi langsung ke database game server.

### 🔐 Authentication System & Profil Pemain
- **Register** — Form pendaftaran dilengkapi dengan validasi input, pencocokan password, hashing menggunakan SHA-1 (standar emulator Aion), dan pengiriman email OTP 6-digit via SMTP (Nodemailer) untuk memverifikasi kepemilikan email.
- **Login** — Sistem login langsung memvalidasi kredensial terhadap tabel `account_data` di database server game. Sistem ini secara otomatis memblokir akses login jika status akun `activated` = 0 (Banned) atau terkunci karena sanksi. Jika berhasil, sistem menerbitkan session token.
- **Email Verification** — Sistem pengecekan OTP. OTP token dienkripsi dan disimpan di tabel `web_email_tokens`.
- **Forgot Password** — Sistem pemulihan kata sandi yang sepenuhnya fungsional menggunakan email recovery token.
- **User Dashboard (`/profile`)** — Menampilkan ringkasan status akun, jumlah karakter, nama karakter, class, level, total Toll (mata uang premium), status membership (VIP/Premium), dan log riwayat sanksi/hukuman jika pernah ada.

### 📰 News & Announcement System (`/news`)
- Admin dapat membuat, mengedit, dan menghapus artikel berita secara langsung dari Admin Panel.
- Fitur penulisan berita mencakup: Judul, konten teks, tag (Update/Event/Maintenance), upload gambar langsung ke server di `/public/img/news`, dan pengaturan tanggal publikasi.
- Halaman publik `/news` menampilkan artikel dengan sistem **Pagination** (maksimal 5 artikel per halaman).
- Sistem **Featured Post**: Artikel terbaru akan selalu otomatis di-highlight dengan ukuran card yang lebih besar dan desain yang lebih premium.
- Validasi wajib gambar untuk setiap berita agar tampilan website tetap profesional.

### 🛒 In-Game Shop, Blackmarket & Limited Items
- Halaman `/shop` menghubungkan web langsung dengan ekonomi in-game. Menampilkan katalog item dari database dengan kategori (Armor, Weapon, Consumable, dll.), filter harga, dan gambar icon yang diekstrak dari client.
- **Sistem Pembelian Langsung:** Pemain dapat membeli item menggunakan mata uang **Toll**. Sistem akan memotong saldo Toll di `account_data` dan menyisipkan item ke tabel `inventory` karakter di game secara real-time.
- **Limited Items (Item Terbatas):** Admin dapat menyetel stok item webshop. Item yang dibeli akan mengurangi stok global, dan jika mencapai 0, item berstatus *Sold Out*.
- **Item Discount (Diskon):** Mendukung pengaturan harga coret dan harga diskon.
- **Blackmarket:** Toko tersembunyi/rotasi spesial (diisi menggunakan `seed_blackmarket.js`) yang menjual item-item unik dengan batas pembelian ketat.
- Semua riwayat transaksi tersimpan rapi di tabel `shop_purchase_history` untuk keperluan audit keuangan.

### 🎰 Gacha / Spin System
- Sistem undian gacha di mana probabilitas drop item (rate) dan hadiah dapat dikonfigurasi sepenuhnya oleh admin.
- Pemain menggunakan mata uang Toll untuk melakukan *spin*.
- Sistem akan mengacak algoritma berbasis persentase rate untuk menentukan hadiah.
- Log hasil gacha disimpan dalam database untuk menghindari eksploitasi dan memudahkan audit staf.

### 🎁 Redeem Code System
- Sistem kode promosi. Admin dapat membuat kode rahasia yang memberikan reward berupa item (berdasarkan Item ID) atau Toll.
- **Limit Penggunaan:** Kode dapat diatur untuk "Single-use" (satu kali pakai untuk satu akun) atau "Multi-use" (dapat diklaim oleh X orang pertama).
- Pemain memasukkan kode di web portal, sistem memvalidasi sisa kuota kode, status akun, dan langsung mengirimkan hadiah ke in-game mailbox atau inventory.

### 📊 Community & Leaderboard Pages
- **Ranking (`/ranking`)** — Leaderboard (Top 100) pemain server. Diurutkan berdasarkan jumlah EXP / Level tertinggi. Data menampilkan Class, Ras (Elyos/Asmodian), Legion (Guild), dan Kill/Death ratio, ditarik dari tabel `players`.
- **Banlist (`/community/banlist`)** — Halaman transparansi atau *Wall of Shame* yang memajang akun-akun pelanggar hukum server. Menampilkan username yang disensor sebagian dan status sanksi dengan badge khusus:
  - 🔒 **Account Restricted (Lock)** — Pelanggaran ringan/investigasi.
  - 🚫 **Account Suspended (Ban)** — Pelanggaran berat/permanen ban.
  - 🖥️ **Hardware Suspended (TPM Ban)** — Banned tingkat hardware.
- **Voting System** — Integrasi API ke situs vote server, pemain mendapatkan reward otomatis saat berhasil vote.

### 🎫 Support & Ticketing System (`/support`)
- Sistem keluhan dan laporan pemain yang lengkap. Pemain dapat membuka tiket untuk melaporkan *bug*, melakukan banding (appeal) ban, melaporkan pemain yang melakukan *exploit*, atau sekadar bertanya.
- Sistem di-handle oleh database khusus `z_zwebaion_support.sql`.
- Status tiket diklasifikasikan menjadi "Open", "In Progress", "Answered", dan "Closed".
- Terdapat fitur percakapan 2-arah antara GM/Staf dan pemain di dalam detail tiket.

### 📜 Lore & Guide Pages (`/guide`)
- Halaman informasi statis (Guide) yang menjelaskan lore server Aion, sistem ras (Elyos vs Asmodian), detail setiap Class, rate server, dan FAQ.
- Desain antarmuka kelas atas yang dibalut dengan animasi masuk, *glassmorphism*, gradient teks, dan efek *shimmer*.

---

## 🖥️ Desktop Launcher (EnvyLauncher - Electron)

Aplikasi desktop kustom mandiri (standalone launcher) berbasis Electron yang menjadi pintu masuk pemain ke dalam server.

### 🎨 Desain UI/UX & Musik Latar
- Window menggunakan ukuran fixed (tidak dapat di-*resize*) **1500 × 850 px**.
- Desain titlebar (bingkai window) custom—menghilangkan default Windows header. Tombol minimize dan close dirancang dengan CSS.
- Tampilan cinematic dengan integrasi background image dan efek animasi overlay gradient. Tipografi menggunakan font premium ("Cinzel").
- Background music (OST Aion) terintegrasi yang memutar lagu secara loop, lengkap dengan kontrol interaktif volume dan tombol Mute/Unmute.
- Tombol Play diberikan efek *Pulse glow*, animasi hover transisi mulus, dan *shimmer effect*.

### 📦 Patcher & File Integrity System
- Launcher secara dinamis menarik daftar manifest file (berupa JSON) dari server web melalui `/api/v1/launcher/manifest`.
- Menggunakan sistem *hashing* **SHA-256**, launcher memindai satu persatu file instalasi client di PC pemain.
- Jika file di PC pemain berbeda *hash*-nya, diubah isinya, atau hilang (missing), launcher akan menandainya dan secara otomatis mengunduh versi yang benar dari Patch Server.
- Progress bar dihitung dan dirender secara real-time berdasarkan persentase total byte yang berhasil didownload, beserta teks nama file yang sedang diproses.

### 🚀 Game Launch, SSO, & Java Server Blocking (Anti-Bypass)
Fitur unggulan proyek ini adalah **Modifikasi Source Code Login Server (Java)** yang memaksa pemain hanya bisa login melalui EnvyLauncher.
- Pemain melakukan login di Launcher. Launcher menghasilkan token OTP unik (Session Token).
- Launcher mengeksekusi file eksekutabel game (`AION.bin`) melalui script argument shell yang meng-inject IP server, port, argument khusus, akun pemain, dan token sesi tersebut.
- Token ditangkap oleh game client dan dikirim ke server Java (`AL-Login`).
- **Strict Bypass Blocker:** Source code `AL-Login` Aion telah dimodifikasi secara drastis untuk menolak metode login password tradisional. Segala upaya meluncurkan game menggunakan file `.bat` konvensional (misalnya: `start aion.bin -ip 127.0.0.1 -cc:1 -noweb`) **tidak akan bisa masuk** karena server hanya menerima validasi session token unik yang dihasilkan dari verifikasi launcher EnvyLauncher kita. Sistem ini mematikan celah masuk paksa (bypass) dari luar ekosistem launcher.

### ⚙️ Launcher Settings Panel
- Menu pengaturan yang memungkinkan pemain memilih direktori instalasi folder game melalui *file browser dialog* native Windows.
- Pengaturan audio untuk mengontrol volume lagu launcher.
- **Tombol Clear Cache:** Secara paksa menghapus seluruh session Electron, *cookies*, dan *localStorage* untuk memperbaiki error tersangkut (stuck) dan melogout akun seketika.

### 🚨 Ban Notification Overlay
Saat login, launcher menampilkan notifikasi full-screen overlay yang berbeda untuk setiap jenis sanksi:
- 🔒 **Account Locked** — Layar transparan oranye dengan pesan penahanan.
- 🚫 **Account Suspended** — Layar merah terang peringatan banned permanen.
- 🖥️ **Hardware Suspended** — Layar merah gelap dengan ikon monitor hancur (TPM Ban), pemain ditegaskan bahwa komputernya tidak dapat mengakses server.

---

## 🛡️ EnvyGuard Anti-Cheat System (Full Ecosystem)

Sistem proteksi multi-lapisan yang membentang dari level Hardware PC, Client-side Code (DLL), hingga Server-side API & Web Dashboard, dirancang dari nol secara custom.

### Lapisan 1: TPM Hardware Identity (PowerShell)
Tujuan dari lapisan ini adalah menciptakan sistem "Hardware Ban" (banned perangkat) yang mustahil untuk dipalsukan (HWID Spoofer-proof).
- **Script `tpm_helper.ps1`:** Launcher mengeksekusi PowerShell yang berinteraksi dengan **Windows CNG (Cryptography Next Generation) API**.
- Kriptografi tidak disimpan di hard disk, melainkan dibaca dan di-generate langsung di dalam silikon **TPM 2.0 Platform Crypto Provider** motherboard.
- **Hardware Device ID** diciptakan dari *SHA-256 hash* terhadap public key RSA (terikat secara fisik ke motherboard keras).
- **Alur Challenge-Response:** 
  1. PC meminta *Challenge* hex string 32-byte acak ke API `/api/v1/launcher/login/challenge`.
  2. PowerShell memaksa chip TPM untuk menandatangani (Sign) *challenge* tersebut dengan *Private Key* TPM.
  3. Hasil tanda tangan digital dikirim kembali ke Web Server (Next.js).
  4. Server memverifikasi signature. Jika gagal, berarti TPM spoofed/fake, akses otomatis ditolak di level launcher, game tidak akan pernah terbuka.

### Lapisan 2: Client-side Memory Scanner (C++ DLL)
Sistem inspeksi lokal yang berjalan paralel saat gamer sedang bermain.
- **`EnvyGuard.cpp` & `EnvyGuard.dll`:** Library native C++ custom yang diinjeksi (hook) langsung ke proses memori `AION.bin`.
- Modul melakukan **Memory Scanning**; memindai string dan pola bit di RAM secara konstan untuk mencari *signature* Cheat Engine, WPE Pro, atau injector pihak ketiga.
- Melakukan verifikasi intergrasi memori klien agar fungsi utama game tidak diedit saat sedang berjalan (menghindari wallhack, speedhack, dll).
- **EnvyGuard Heartbeat & Telemetri:** DLL ini secara konstan mem-ping backend `/api/v1/anticheat` setiap sekian detik, membawa informasi aktivitas.
- Jika pemain secara paksa men-suspend atau men-kill thread EnvyGuard.dll dari Task Manager, heartbeat akan mati, dan Web API akan men-trigger Auto-Kick/Lock pada akun tersebut dari Game Server.

### Lapisan 3: Web-side Live Monitoring & Database Architecture
Segala hasil dari C++ dan TPM masuk ke database khusus yang sangat tertata:

```sql
-- Identitas hardware terdaftar
CREATE TABLE tpm_identities (
  device_id VARCHAR(255) PRIMARY KEY,
  public_key TEXT NOT NULL,
  key_algorithm VARCHAR(50),
  created_at TIMESTAMP,
  last_seen TIMESTAMP
);

-- Daftar hardware TPM yang diblokir (Hardware Ban)
CREATE TABLE banned_tpm (
  id INT AUTO_INCREMENT PRIMARY KEY,
  device_id VARCHAR(255) NOT NULL,
  account_id INT,
  reason TEXT,
  banned_at TIMESTAMP
);

-- Log anti-cheat yang dikirimkan oleh C++ DLL EnvyGuard
CREATE TABLE anticheat_logs (
  id INT AUTO_INCREMENT PRIMARY KEY,
  account_id INT,
  event_type VARCHAR(100),
  severity ENUM('LOW','MEDIUM','HIGH','CRITICAL'),
  evidence TEXT,
  created_at TIMESTAMP
);
```

- **Sistem Enforcement Otomatis:** Saat GM menekan tombol "TPM Ban" di web panel, `device_id` pemain ditambahkan ke `banned_tpm`. Begitu pemain mencoba buka launcher ulang (bahkan kalau dia bikin akun Aion yang baru dari website), API TPM Validation akan gagal karena Device ID-nya ada di `banned_tpm`, menghasilkan layar "Hardware Suspended" permanen tanpa jalan pintas.

---

## 👨‍💼 Admin Panel & Management Backend (`/admin`)

Halaman khusus kontrol eksekutif dan manajemen operasional penuh untuk pemilik server (Super Admin) dan Game Master (GM). Dilengkapi perlindungan route berlapis.

### 🔐 Access Level Control (`/admin/access`)
Staf diklasifikasikan dengan sistem Role Level 1 hingga 5:
- **Level 1 (Moderator):** Hanya akses baca (read-only) terhadap Anti-Cheat Logs dan Banlist.
- **Level 3 (Game Master):** Memiliki hak untuk membalas dan menutup tiket Support, serta membuat Redeem Code.
- **Level 5 (Super Admin):** Memegang kendali absolut. Dapat memodifikasi konfigurasi launcher, menyunting artikel berita, mengubah toko (Webshop), mem-banned/unban pemain hingga tingkat hardware TPM, dan menaikkan/menurunkan level akses staf lain.

### 📋 Manage Users & Punishment (`/admin/users`)
- Panel direktori seluruh akun di server yang dilengkapi pencarian instan.
- Menampilkan data sensitif pemain: **Alamat IP Terakhir**, **Alamat MAC**, dan **TPM Device ID**.
- Memiliki deretan tombol *Action*: **Lock**, **Ban**, **TPM Ban**, **Unban**, **Flag** (untuk menandai akun mencurigakan), dan **Send Mail** (mengirim item langsung ke kotak surat in-game pemain).
- **Sistem UX Pencegahan Kesalahan:** Setiap tindakan sanksi dilindungi dengan konfirmasi *modal pop-up*. Warna informasinya berbeda; Lock (Oranye), Ban (Merah terang), TPM Ban (Merah gelap pekat dengan peringatan penghancuran akses perangkat).
- Setiap tindakan mencatatkan waktu dan alasan pada riwayat (*Punishment History*).

### 🕵️ Anti-Cheat Live Monitoring (`/admin/anticheat-logs`)
- Membaca telemetri yang dikirimkan oleh `EnvyGuard.dll` secara real-time.
- Memungkinkan staf memfilter log berdasarkan tingkat *Severity* atau tipe *Event* (mis. 'Memory Scan Failed', 'Heartbeat Timeout', 'Suspicious Module').
- **Fitur Live Investigation:** Terdapat tab "Under Investigation". Staf dapat memasukkan ID atau mengklik bendera (Flag) pada akun tersangka. Sistem admin kemudian akan melakukan *polling* (refresh otomatis HTTP) setiap 5 detik ke API server Aion untuk melihat secara live apakah karakter dari akun tersebut sedang online/offline di dalam game.
- Tersedia tombol akses instan untuk **Kick** pemain (menendang mereka dari server seketika) atau memberi **TPM Ban** dari halaman tabel telemetri yang sama.

### ⚙️ Launcher & Patch Configuration (`/admin/launcher`)
- Mengubah versi manifest server secara dinamis dari web, yang akan memaksa seluruh pemain yang membuka launcher untuk mendownload versi *update* baru.

### 🛍️ Manajemen Webshop & Blackmarket (`/admin/shop`)
- Interface form penuh untuk pembuatan barang jualan: Input Item ID Game, Nama Item, Deskripsi, Kategori, Harga Normal, Harga Diskon, dan batas limit inventaris (Limited Item).
- Manajemen gambar: Upload icon barang yang disimpan ke folder server.

### 📰 Manajemen Berita (`/admin/news`)
- CRUD penuh (Create, Read, Update, Delete) artikel berita portal.

### 🎟️ Manajemen Redeem Code (`/admin/redeem`)
- Pembangkitan kode acak atau *custom vanity code* (misalnya: `WELCOMESERVER2026`).
- Mengaitkan jumlah item ID yang akan didapatkan pemain saat mereka mengeklaimnya.

### 🎫 Manajemen Support (`/admin/reports`)
- Inboks terpusat bagi GM untuk merespons keluhan pemain. GM dapat menambahkan catatan (Notes) internal antar staf dan merubah status laporan dari *Open* ke *Closed*.

### 📊 Admin Dashboard
- Metrik sistem yang mencakup total akun terdaftar, total pemain online, jumlah akun yang terkena larangan, dan grafik riwayat pembelian.

---

## 🔌 API Architecture (v1)

Semua sistem komunikasi antara Game Client, Launcher, Web Panel, dan Database berjalan secara statis di atas arsitektur **Next.js App Router API Routes (`/src/app/api/v1/`)**. API dibangun menggunakan TypeScript murni.

| Modul Direktori | Penjelasan Lengkap Endpoint |
|---|---|
| **`/api/v1/account/`** | Melayani pengambilan detil akun, pembaruan password, pembacaan token. |
| **`/api/v1/admin/`** | Endpoint masif yang diamankan verifikasi hak akses staf. Rute: `/admin/users`, `/admin/users/punish` (eksekusi larangan), `/admin/users/history`, `/admin/users/flag`, `/admin/news` (pembuatan pos), `/admin/launcher` (set patch version), `/admin/support` (balas tiket). |
| **`/api/v1/anticheat/`** | Endpoint reserver penerima paket *telemetri* dan injeksi dari *EnvyGuard C++ DLL* serta pencatatan log. |
| **`/api/v1/auth/`** | Endpoint otentikasi inti. Rute: `/auth/register`, `/auth/login`, `/auth/verify` (verifikasi email OTP), `/auth/forgot-password`. |
| **`/api/v1/community/`** | Data interaktif publik: `/community/banlist`, `/community/ranking` fetching logika perankingan real-time database game. |
| **`/api/v1/game/`** | Fetch data kondisi server (`Online`/`Offline`), hitung player in-game. |
| **`/api/v1/item/`** | Ekstensi parsing data ID Item dari klien game ke nama dan icon. |
| **`/api/v1/launcher/`** | Pusat komando Electron. Rute: `/launcher/manifest`, `/launcher/device/register` (penyimpanan public key TPM PC), `/launcher/login/challenge` (generasi tantangan cryptografi). |
| **`/api/v1/news/`** | Fetch dan render artikel untuk publik dengan fungsi pagination parameter url. |
| **`/api/v1/ranking/`** | Pengambilan statistik PVP dan EXP leaderboard pemain Aion. |
| **`/api/v1/server/`** | Variabel konfigurasi lingkungan dan maintenance mode flag. |
| **`/api/v1/shop/`** | Logika transaksi pembelian. Mengurangi saldo Toll pemain, menambah rekaman ke `purchase_history`, dan pengiriman Mail item ke database game. Cek stok Limited Item. Eksekusi probabilitas putaran Gacha. |
| **`/api/v1/support/`** | Endpoint portal pembuatan tiket dukungan oleh sisi klien pemain. |
| **`/api/v1/user/`** | Manajemen status *cookie*, JWT, manipulasi *session*, logout logika. |

---

## 🗄️ Database Architecture & Setup Automation

Proyek ini sangat kompleks sehingga membutuhkan **dua database mandiri** yang berjalan serentak. Karena keduanya berjalan di daemon MySQL (XAMPP) yang sama, sistem saya memungkinkan dilakukannya kueri lintas-*database* (Cross-database JOIN) secara instan.

| Skema Utama Database Game (`z_zaion`) | Skema Utama Database Web & Custom (`z_zwebaion`) |
|---|---|
| • `account_data` (Data login inti game) <br> • `players` (Data setiap karakter) <br> • `legions` (Sistem guild in-game) <br> • `inventory` & `mailbox` (Penyimpanan item) | • `tpm_identities` (Penyimpanan kunci kriptografi Hardware PC) <br> • `banned_tpm` (Daftar HWID terblokir) <br> • `shop_items` (Toko web + Limited Stock + Diskon) <br> • `shop_purchase_history` (Riwayat transaksi toll) <br> • `support_tickets` (Sistem tiket & komplain pemain) <br> • `account_punishments` (Riwayat ban/lock) <br> • `news` (Portal artikel web) <br> • `launcher_sessions` (Cache login) <br> • `web_email_tokens` (Data OTP) |

### Skrip Node.js - Automasi Setup Database Penuh
Untuk meniadakan keharusan *import SQL manual* yang repot, semua pembuatan tabel dan skema telah saya **otomatisasi** melalui penulisan barisan file *Javascript murni* (dieksekusi dengan Node.js) yang diletakkan di Root Folder. Staf hanya perlu menjalankan skrip ini sekali saat inisialisasi:
1. `create_access_db.js` — Membangun level admin staf (Hak Akses).
2. `create_flags_table.js` — Membangun tabel penanda investigasi anti-cheat.
3. `create_launcher_tables.js` — Menyusun arsitektur otentikasi HWID TPM.
4. `create_mail_table.js` — Menyambung jembatan API kirim paket in-game.
5. `create_punishments_table.js` — Tabel riwayat pelanggaran server.
6. `create_redeem_tables.js` — Relasi kode promo dan log klaim limitasi.
7. `create_reports_table.js` — Struktur dukungan dan tiket pemain.
8. `create_settings_table.js` — Variabel konfigurasi lingkungan dinamis.
9. `seed_blackmarket.js` — Menyisipkan (seeding) daftar katalog item *limited* ke toko Blackmarket.
10. Serta barisan kode `test_db_schema.js`, `test_query.ts` untuk verifikasi struktur tabel (unit-testing). Terdapat juga folder `/sql/` cadangan (seperti `z_zwebaion_access.sql`, `z_zwebaion_news.sql`, `z_zwebaion_redeem.sql`, `z_zwebaion_support.sql`, `z_zwebaion_webshop.sql`).

---

## 🏗️ Project Structure (The Monorepo)

Semua struktur saling bersambung di satu *monorepo* utama:

```
pow/
├── src/                      ← Source code Backend & Frontend Web (Next.js)
│   ├── app/
│   │   ├── (public pages)/   ← news/, shop/, ranking/, community/, login/, register/, guide/, support/
│   │   ├── admin/            ← users/, anticheat-logs/, news/, redeem/, shop/, access/, launcher/
│   │   └── api/v1/           ← Direktori lengkap REST API (berisi 14 modul endpoint di atas)
│   ├── components/           ← Komponen Visual React UI (TopNav, Modals, Forms)
│   └── lib/
│       ├── db.ts             ← Instance koneksi kolam (pool) MySQL
│       └── email.ts          ← Pengaturan SMTP Mailer
├── EnvyLauncher/             ← Workspace Source Code Desktop Electron
│   ├── src/App.tsx           ← Komponen Frontend Launcher (React)
│   ├── electron-main.cjs     ← File Kernel Electron (Patcher, Launch Game, IPC handlers)
│   └── tpm_helper.ps1        ← Script Native Sistem Kripto Windows PowerShell
├── Launcher + Anti Cheat/    ← Source Code Sistem C++
│   └── EnvyGuard/            ← (EnvyGuard.cpp, EnvyGuard.dll, Sistem Memory Scanner)
├── sql/                      ← Arsip Struktur File MySQL Cadangan (.sql)
└── (Berbagai file .js)       ← create_launcher_tables.js, seed_blackmarket.js, dll (Database Setup)
```

---

## 🎯 Kesimpulan: Pencapaian Teknis & Role

Proyek **Aion: Dark Ascension** ini adalah perwujudan dari integrasi sistem komputasi perangkat keras tingkat rendah dengan aplikasi web tingkat tinggi. Fitur yang paling bersinar adalah implementasi arsitektur **Keamanan Lintas Platform** yang mengawinkan:
1. Pengecekan Integritas Perangkat Keras menggunakan sensor TPM 2.0 via skrip Windows PowerShell.
2. Pencegahan Injeksi Kode Memori via *Native C++ DLL Hook*.
3. **Modifikasi Source Code Server Java Aion** untuk menolak segala bentuk bypass file `.bat`, mengunci akses game ketat hanya dari token launcher buatan kita.
4. Portal Aplikasi Web canggih berbasis React/Next.js dengan SSO Login Session.
5. Database relasional ganda (Game dan Website) yang berkomunikasi secara *real-time* tanpa waktu jeda server.
6. Pengecekan limitasi toko web (Limited Item/Blackmarket), Gacha dengan probabilitas matematika, Sistem Redeem Promo secara dinamis, dan sistem Ticketing komprehensif.
7. Administrasi Panel level enterprise dengan Live Monitoring Anti-Cheat (polling API 5-detik) dan warna UX interaktif agar GM tidak salah *banned* orang.

**Status Proyek:** Fully Completed & Production-ready.  
**Durasi Pembuatan:** Cuma **5 Hari** (Pengembangan ekstrim secara terus menerus).  
**Role:** Lead Full-Stack Developer, Game/Systems Security Architect, Database Administrator, UI/UX Designer. Semuanya dikerjakan secara individual dari dasar.

---

> **Disclaimer:** Proyek berskala masif ini didukung dan diasistensi secara penuh oleh **Antigravity AI**, yang membantu mempercepat proses *development* dari konseptualisasi kode hingga tahap eksekusi akhir dalam waktu singkat.
