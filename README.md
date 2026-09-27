# dashboard-vue

Proyek Vue 3 untuk mata kuliah **Web Application Development** — Session 5 (Composition API).

## Cara Menjalankan

```bash
npm install
npm run dev
```

Buka `http://localhost:5173` di browser.

## Fitur yang Diimplementasikan

### Session 5 (Practicum)
- ✅ Vue 3 project dengan Vite
- ✅ Single File Component (SFC) dengan `<template>`, `<script setup>`, `<style scoped>`
- ✅ Props pada `UserCard.vue` (`user: Object`)
- ✅ `ref()` untuk state (`users`, `keadaan`)
- ✅ Fetch data dari JSONPlaceholder API
- ✅ `v-for` + `:key="user.id"` untuk render daftar
- ✅ `v-if / v-else-if / v-else` untuk 4 state (idle, loading, empty, error, success)
- ✅ `computed()` untuk `penggunaTersaring` (search filter)
- ✅ `watch()` untuk `riwayatPencarian`

### Technical Assignment 2

**Problem 1 — Detail Toggle (Local State)**
- Tombol "Lihat Detail" / "Sembunyikan" di setiap kartu
- State `detailTerbuka` adalah `ref` lokal di `UserCard.vue`, bukan dari `App.vue`
- Menampilkan phone, company.name, address.city saat dibuka
- Membuka detail satu kartu tidak mempengaruhi kartu lain

**Problem 2 — Sort Control (Chained Computed)**
- Dua tombol: "Urutkan A-Z" dan "Urutkan Z-A"
- `arahUrutan` ref menyimpan arah sort yang aktif (`'asc'` / `'desc'`)
- `penggunaTerurut` computed bersumber dari `penggunaTersaring` (bukan `users` langsung) → ini "chained computed"
- Sorting menggunakan `[...array].sort()` — spread untuk tidak mutasi array asli
- Tombol aktif ditandai secara visual dengan `:class="{ active: arahUrutan === 'asc' }"`
- `v-for` pada `<UserCard>` membaca dari `penggunaTerurut`

### Additional Exercises
- **Exercise A**: `jumlahHasil` computed — menampilkan "Menampilkan X dari Y pengguna"
- **Exercise B**: Prop `sorotan` (Boolean) pada `UserCard.vue` — background kuning untuk exact-match username
