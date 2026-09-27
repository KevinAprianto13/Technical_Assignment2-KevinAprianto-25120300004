<script setup>
import { ref } from "vue";

// ─────────────── Props ───────────────
defineProps({
  user: {
    type: Object,
    required: true,
  },
  // Exercise B: prop sorotan untuk highlight kartu yang exact-match
  sorotan: {
    type: Boolean,
    default: false,
  },
});

// ─────────────── Problem 1: Local State – Detail Toggle ───────────────
// ref ini dideklarasikan DI DALAM UserCard.vue sendiri (bukan dari App.vue)
// sehingga setiap kartu punya status buka/tutup-nya masing-masing
const detailTerbuka = ref(false);

function toggleDetail() {
  detailTerbuka.value = !detailTerbuka.value;
}
</script>

<template>
  <li class="user-card" :class="{ sorotan: sorotan }">
    <!-- Info Utama -->
    <div class="card-main">
      <div class="card-info">
        <strong class="user-name">{{ user.name }}</strong>
        <span class="user-email">{{ user.email }}</span>
        <span class="user-username">@{{ user.username }}</span>
      </div>

      <!-- Problem 1: Tombol toggle detail -->
      <button class="btn-detail" @click="toggleDetail">
        {{ detailTerbuka ? "Sembunyikan" : "Lihat Detail" }}
      </button>
    </div>

    <!-- Problem 1: Detail info — ditampilkan hanya saat detailTerbuka === true -->
    <div v-if="detailTerbuka" class="card-detail">
      <div class="detail-item">
        <span class="detail-icon">📞</span>
        <span>{{ user.phone }}</span>
      </div>
      <div class="detail-item">
        <span class="detail-icon">🏢</span>
        <span>{{ user.company.name }}</span>
      </div>
      <div class="detail-item">
        <span class="detail-icon">🏙️</span>
        <span>{{ user.address.city }}</span>
      </div>
    </div>
  </li>
</template>

<style scoped>
.user-card {
  display: flex;
  flex-direction: column;
  padding: 0.9rem 1.2rem;
  border-bottom: 1px solid #eee;
  background: white;
  transition: background 0.15s;
}

.user-card:last-child {
  border-bottom: none;
}

.user-card:hover {
  background: #f9f9f9;
}

/* Exercise B: Highlight kartu yang sorotan = true (exact match username) */
.user-card.sorotan {
  background: #fffde7;
  border-left: 4px solid #f6c90e;
}

/* ── Card Main Row ── */
.card-main {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}

.card-info {
  display: flex;
  flex-direction: column;
  gap: 0.15rem;
}

.user-name {
  font-size: 0.95rem;
  font-weight: 600;
  color: #1a1a1a;
}

.user-email {
  font-size: 0.82rem;
  color: #666;
}

.user-username {
  font-size: 0.78rem;
  color: #1a7a9e;
  font-weight: 500;
}

/* Problem 1: Tombol Lihat Detail / Sembunyikan */
.btn-detail {
  flex-shrink: 0;
  padding: 0.35rem 0.85rem;
  border: 2px solid #0b4f6c;
  border-radius: 6px;
  background: transparent;
  color: #0b4f6c;
  font-size: 0.8rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
  white-space: nowrap;
}

.btn-detail:hover {
  background: #0b4f6c;
  color: white;
}

/* ── Detail Panel (Problem 1) ── */
.card-detail {
  margin-top: 0.75rem;
  padding: 0.75rem 1rem;
  background: #f0f7fb;
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
}

.detail-item {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  font-size: 0.85rem;
  color: #333;
}

.detail-icon {
  font-size: 1rem;
  width: 1.2rem;
  text-align: center;
}
</style>
