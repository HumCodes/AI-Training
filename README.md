# AI GitHub Agent

Web app chat berbasis AI (rule-based command parser) untuk mengelola repository GitHub langsung dari browser — 100% client-side, tanpa backend/server.

## Cara Pakai

1. Buka `public/index.html` di browser (bisa langsung double-click, atau upload ke hosting statis seperti zya.me / Vercel / GitHub Pages).
2. Buat **Personal Access Token (PAT)** di GitHub:
   - Klik link "Buat token baru" di sidebar, atau buka https://github.com/settings/tokens/new
   - Pilih scope **`repo`** (untuk akses penuh ke repo privat & publik)
   - Generate, lalu copy token (`ghp_...`)
3. Paste token ke kolom **Personal Access Token** di sidebar, klik **Hubungkan**.
4. Token disimpan di `localStorage` browser kamu sendiri — tidak pernah dikirim ke server manapun selain `api.github.com`.

## Mengapa Tanpa Backend (server.js)?

GitHub REST API mendukung **CORS** dan autentikasi via **Personal Access Token**, sehingga browser bisa langsung memanggil `api.github.com` tanpa perantara server. Backend Express hanya diperlukan untuk OAuth App "resmi" (karena client secret tidak boleh ada di client) — tapi untuk kebutuhan personal seperti ini, PAT jauh lebih sederhana, gratis hosting statis, dan tetap aman karena token tidak pernah keluar dari browser kamu.

Kalau suatu saat butuh OAuth App publik (login dengan tombol "Login with GitHub" untuk banyak user), backend Express tetap bisa ditambahkan — tinggal bilang ya.

## Daftar Perintah

| Perintah | Fungsi |
|---|---|
| `list repo` / `daftar repo` | Tampilkan semua repository |
| `select repo [nama]` / `pilih repo [nama]` | Pilih repo aktif |
| `list file [path]` / `daftar file [path]` | Lihat struktur file/folder |
| `baca file [path]` / `read file [path]` | Baca isi file |
| `buat file [path] dengan isi: [konten]` | Buat/update file (masuk staging) — boleh pakai blok kode ` ```lang ... ``` ` |
| `delete file [path]` / `hapus file [path]` | Hapus file (masuk staging) |
| `commit [pesan]` | Eksekusi semua perubahan staging ke GitHub dengan pesan commit tersebut |
| `push [branch]` | Set branch target untuk commit selanjutnya (lihat catatan di bawah) |
| `buat branch [nama] dari [base]` | Buat branch baru dari branch lain |
| `merge [branch] ke [target]` | Merge satu branch ke branch lain |
| `status` | Cek status koneksi, repo aktif, branch aktif, jumlah staging |
| `clear` | Bersihkan chat |
| `help` | Tampilkan semua perintah |

### Catatan soal `commit` & `push`

GitHub Contents API tidak punya konsep "local repo" — setiap `commit` langsung mengubah file di GitHub (otomatis "ter-push"). Jadi alur kerjanya:

1. `buat file ...` / `delete file ...` → masuk **staging** (belum dikirim ke GitHub)
2. `commit [pesan]` → semua perubahan staging dikirim ke GitHub sekaligus, dengan konfirmasi sebelum eksekusi
3. `push [branch]` → cuma mengganti branch target untuk commit selanjutnya (atau buat branch baru dulu kalau belum ada)

## Shortcut Keyboard

- `Enter` → kirim pesan, `Shift+Enter` → baris baru
- `Ctrl+K` → command palette (fuzzy search semua perintah)
- `Ctrl+/` → bantuan
- `↑ / ↓` → riwayat input sebelumnya

## Fitur Tambahan

- Drag & drop file teks/kode ke chat → otomatis masuk staging
- Tombol voice input (Web Speech API, bahasa Indonesia)
- Activity log (riwayat commit/branch/merge, tersimpan di localStorage)
- File preview modal dengan syntax highlighting
- Responsive (sidebar 300px desktop, 260px tablet, off-canvas di mobile)

## Batasan yang Perlu Diketahui

- File besar (>1MB) tidak bisa dibaca via Contents API (limitasi GitHub)
- Upload file biner (gambar, zip, dll) belum didukung — hanya file teks/kode
- "AI" di sini adalah **command parser berbasis pola/regex** bilingual (ID/EN), bukan LLM generatif — sesuai requirement "100% client-side, no external API". Kalau mau upgrade ke LLM sungguhan (misalnya pakai pola dari project Aether Chat kamu), tinggal tambahkan provider API key opsional sebagai fallback parser.
-
