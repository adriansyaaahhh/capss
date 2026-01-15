<template>
  <div class="page">
    <h1 class="title">Riwayat Laporan Selesai</h1>

    <div v-if="riwayat.length === 0" class="empty">
      Belum ada laporan yang diselesaikan
    </div>

    <div
      v-for="item in riwayat"
      :key="item.id"
      class="card"
      @click="goDetail(item.id)"
    >
      <div class="left">
        <img :src="item.fotoAfter" class="thumb" />
      </div>

      <div class="right">
        <h3>{{ item.judul }}</h3>
        <p class="lokasi">{{ item.lokasi }}</p>

        <div class="meta">
          <span class="badge">SELESAI</span>
          <span class="date">{{ item.tanggalSelesai }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "DinasRiwayatSelesai",
  data() {
    return {
      riwayat: [],
    };
  },
  mounted() {
    this.riwayat =
      JSON.parse(localStorage.getItem("riwayat_selesai")) || [];
  },
  methods: {
    goDetail(id) {
      this.$router.push(`/dinas/riwayat-selesai/${id}`);
    },
  },
};
</script>

<style scoped>
.page {
  max-width: 1000px;
  margin: 0 auto;
}

.title {
  margin-bottom: 24px;
}

.card {
  display: flex;
  gap: 16px;
  background: #fff;
  border-radius: 16px;
  padding: 16px;
  margin-bottom: 16px;
  cursor: pointer;
  box-shadow: 0 8px 22px rgba(0,0,0,0.05);
  transition: 0.2s;
}

.card:hover {
  transform: translateY(-2px);
}

.thumb {
  width: 110px;
  height: 80px;
  border-radius: 12px;
  object-fit: cover;
}

.lokasi {
  font-size: 14px;
  color: #64748b;
}

.meta {
  display: flex;
  gap: 10px;
  margin-top: 8px;
}

.badge {
  background: #dcfce7;
  color: #166534;
  font-size: 11px;
  padding: 4px 10px;
  border-radius: 999px;
  font-weight: 700;
}

.date {
  font-size: 12px;
  color: #64748b;
}

.empty {
  text-align: center;
  color: #94a3b8;
}
</style>
