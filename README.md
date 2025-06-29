# To-Do List App (Vue 3, Axios, JSON Server)

Aplikasi **To-Do List** sederhana yang dibangun dengan **Vue 3 Composition API**, menggunakan **Axios** untuk interaksi API, dan **JSON Server** sebagai backend REST API palsu. Aplikasi ini mendukung operasi **CRUD** (Create, Read, Update, Delete) penuh untuk manajemen tugas.

---

## ✨ Fitur

- **Tambahkan To-Do Baru**  
  Mudah menambahkan tugas baru ke daftar Anda.

- **Lihat To-Do**  
  Menampilkan semua tugas yang ada.

- **Tandai Selesai/Belum Selesai**  
  Tandai tugas sebagai selesai atau belum selesai dengan sekali klik.

- **Edit To-Do**  
  Perbarui judul tugas langsung di daftar.

- **Hapus To-Do**  
  Hapus tugas yang tidak diperlukan lagi.

- **Penanganan Loading & Error**  
  Memberikan umpan balik visual saat memuat data atau terjadi kesalahan.

- **Desain Responsif**  
  Tampilan yang dioptimalkan untuk perangkat desktop dan mobile.

---

## 🛠️ Teknologi yang Digunakan

- **Vue 3 (Composition API)** – Framework JavaScript progresif untuk membangun antarmuka pengguna.
- **Axios** – Klien HTTP berbasis Promise untuk membuat permintaan API.
- **JSON Server** – Server REST API palsu yang cepat untuk prototyping dan pengembangan frontend.
- **HTML5 & CSS3** – Untuk struktur dan gaya aplikasi.

---

## 📋 Persyaratan

Pastikan Anda memiliki hal-hal berikut sebelum menjalankan proyek:

- **Node.js** (disarankan versi LTS)
- **npm** atau **Yarn**

---

## 🚀 Instalasi & Menjalankan Proyek

Ikuti langkah-langkah berikut untuk menjalankan aplikasi di lingkungan lokal Anda.

### 1. Klon Repositori (Opsional)

Jika proyek ini dari GitHub, klon terlebih dahulu:

```bash
git clone <URL_REPOSITORI_ANDA>
cd <NAMA_FOLDER_PROYEK_ANDA>
```

### 2. Instal Dependensi Proyek

Di direktori proyek Anda:

```bash
npm install
# atau
yarn install
```

### 3. Siapkan JSON Server

Instal `json-server` secara lokal sebagai dev dependency:

```bash
npm install -D json-server
# atau
yarn add -D json-server
```

Buat file `db.json` di root proyek dengan isi berikut:

```json
{
  "todos": [
    { "id": "1", "title": "Belajar Vue 3", "completed": false },
    { "id": "2", "title": "Membangun Aplikasi To-Do List", "completed": true },
    { "id": "3", "title": "Minum kopi", "completed": false }
  ]
}
```

### 4. Jalankan JSON Server

Buka terminal baru dan jalankan:

```bash
json-server --watch db.json --port 3000
```

Server akan tersedia di: `http://localhost:3000/todos`  
Pastikan `API_URL` di `TodoList.vue` disesuaikan dengan alamat ini.

### 5. Jalankan Aplikasi Vue

Kembali ke terminal utama:

```bash
npm run dev
# atau
yarn dev
```

Aplikasi akan berjalan di: `http://localhost:5173` (atau port yang tersedia). Buka di browser Anda.

---
