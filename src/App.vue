<script setup>
import { ref, computed, watch } from "vue";
import UserCard from "./components/UserCard.vue";

const judul = "Dashboard Vue – Pertemuan 5";

// ─────────────── State Utama ───────────────
const users = ref([]);
const keadaan = ref("idle"); // idle | loading | empty | error | success

// ─────────────── Muat Data dari API ───────────────
async function muatPengguna() {
  keadaan.value = "loading";
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/users");
    if (!response.ok) {
      throw new Error("Status HTTP: " + response.status);
    }
    const data = await response.json();
    if (data.length === 0) {
      keadaan.value = "empty";
      return;
    }
    users.value = data;
    keadaan.value = "success";
  } catch (error) {
    console.error("Gagal memuat data:", error);
    keadaan.value = "error";
  }
}

// ─────────────── Step 8: Search (computed + watch) ───────────────
const queryPencarian = ref("");
const riwayatPencarian = ref([]);

const penggunaTersaring = computed(() => {
  const q = queryPencarian.value.toLowerCase().trim();
  if (!q) return users.value;
  return users.value.filter((user) =>
    user.username.toLowerCase().includes(q)
  );
});

watch(queryPencarian, (nilaiBaru) => {
  if (nilaiBaru.trim() !== "") {
    riwayatPencarian.value.push(nilaiBaru);
  }
});

// ─────────────── Problem 2: Sort Control (Chained Computed) ───────────────
// ref untuk menyimpan arah urutan yang aktif
const arahUrutan = ref("asc"); // 'asc' | 'desc'

// Computed BARU yang bersumber dari penggunaTersaring (bukan langsung users)
// Ini adalah "chained computed": filter dulu, baru di-sort
const penggunaTerurut = computed(() => {
  // spread [...array] supaya tidak mutasi array asli di dalam computed
  return [...penggunaTersaring.value].sort((a, b) => {
    if (arahUrutan.value === "asc") {
      return a.name.localeCompare(b.name);
    } else {
      return b.name.localeCompare(a.name);
    }
  });
});

// ─────────────── Exercise A: Jumlah Hasil ───────────────
const jumlahHasil = computed(() => penggunaTersaring.value.length);
</script>

<template>
  <header class="app-header">
    <h1>{{ judul }}</h1>
  </header>

  <main class="app-main">
    <!-- Tombol Muat & Search -->
    <!-- input v-model="queryPencarian" diletakkan SEBELUM chain v-if (sesuai Step 8.3 PDF) -->
    <div class="controls-top">
      <button class="btn btn-muat" @click="muatPengguna">
        Muat Pengguna
      </button>
      <input
        v-model="queryPencarian"
        type="text"
        placeholder="Cari username..."
        class="input-search"
      />
    </div>

    <!-- Status States — v-if/v-else-if chain tidak boleh terputus (sesuai PDF Session 5) -->
    <p v-if="keadaan === 'idle'" class="state-message idle">
      Klik tombol "Muat Pengguna" untuk memulai.
    </p>
    <p v-else-if="keadaan === 'loading'" class="state-message loading">
      Memuat data...
    </p>
    <p v-else-if="keadaan === 'empty'" class="state-message empty">
      Tidak ada pengguna ditemukan.
    </p>
    <p v-else-if="keadaan === 'error'" class="state-message error">
      Gagal memuat data. Coba lagi.
    </p>

    <!-- SUCCESS state: sort controls + info jumlah + daftar -->
    <template v-else-if="keadaan === 'success'">
      <!-- Problem 2: Tombol "Urutkan A-Z" dan "Urutkan Z-A" -->
      <div class="sort-controls">
        <span class="sort-label">Urutkan:</span>
        <button
          class="btn btn-sort"
          :class="{ active: arahUrutan === 'asc' }"
          @click="arahUrutan = 'asc'"
        >
          Urutkan A-Z
        </button>
        <button
          class="btn btn-sort"
          :class="{ active: arahUrutan === 'desc' }"
          @click="arahUrutan = 'desc'"
        >
          Urutkan Z-A
        </button>
      </div>

      <!-- Exercise A: jumlahHasil — "Menampilkan X dari Y pengguna" -->
      <p class="info-jumlah">
        Menampilkan <strong>{{ jumlahHasil }}</strong> dari
        <strong>{{ users.length }}</strong> pengguna
      </p>

      <!-- v-for dari penggunaTerurut (Problem 2 — chained computed) -->
      <ul class="user-list">
        <UserCard
          v-for="user in penggunaTerurut"
          :key="user.id"
          :user="user"
          :sorotan="user.username.toLowerCase() === queryPencarian.toLowerCase().trim() && queryPencarian.trim() !== ''"
        />
      </ul>
    </template>
  </main>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');

* {
  font-family: 'Inter', sans-serif;
  box-sizing: border-box;
}

.app-header {
  padding: 1.5rem 2rem;
  background: linear-gradient(135deg, #0b4f6c 0%, #1a7a9e 100%);
  color: white;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

.app-header h1 {
  margin: 0;
  font-size: 1.6rem;
  font-weight: 700;
}

.app-main {
  padding: 1.5rem 2rem;
  max-width: 700px;
  margin: 0 auto;
}

.controls-top {
  display: flex;
  gap: 0.75rem;
  align-items: center;
  flex-wrap: wrap;
  margin-bottom: 1rem;
}

.btn {
  padding: 0.55rem 1.1rem;
  border: none;
  border-radius: 8px;
  font-size: 0.9rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
}

.btn-muat {
  background: #0b4f6c;
  color: white;
}

.btn-muat:hover {
  background: #1a7a9e;
}

.input-search {
  flex: 1;
  min-width: 180px;
  padding: 0.55rem 0.9rem;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 0.9rem;
  transition: border-color 0.2s;
  outline: none;
}

.input-search:focus {
  border-color: #1a7a9e;
}

/* ── Problem 2: Sort Controls ── */
.sort-controls {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 0.75rem;
  flex-wrap: wrap;
}

.sort-label {
  font-size: 0.85rem;
  font-weight: 600;
  color: #555;
}

.btn-sort {
  background: #f0f0f0;
  color: #444;
  border: 2px solid #ddd;
  font-size: 0.85rem;
  padding: 0.4rem 0.9rem;
}

.btn-sort:hover {
  background: #e0e0e0;
}

/* Tombol sort yang sedang aktif — visually highlighted (Problem 2) */
.btn-sort.active {
  background: #0b4f6c;
  color: white;
  border-color: #0b4f6c;
  box-shadow: 0 2px 6px rgba(11, 79, 108, 0.35);
}

/* ── Exercise A: Info Jumlah ── */
.info-jumlah {
  font-size: 0.85rem;
  color: #666;
  margin-bottom: 0.75rem;
}

/* ── State Messages ── */
.state-message {
  padding: 1rem 1.25rem;
  border-radius: 10px;
  font-weight: 500;
  margin-top: 1rem;
}

.state-message.idle    { background: #f5f5f5; color: #666; }
.state-message.loading { background: #e8f4fd; color: #1a7a9e; }
.state-message.empty   { background: #fff8e1; color: #b7791f; }
.state-message.error   { background: #fdecea; color: #c0392b; }

/* ── User List ── */
.user-list {
  list-style: none;
  padding: 0;
  margin: 0;
  border-radius: 12px;
  overflow: hidden;
  border: 1px solid #e8e8e8;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.06);
}
</style>
