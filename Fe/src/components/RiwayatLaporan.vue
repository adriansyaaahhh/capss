<template>
  <div class="page">
    <p class="breadcrumb">Beranda / Riwayat</p>

    <h1 class="title">Riwayat Laporan Anda</h1>

    <!-- SUMMARY -->
    <div class="summary">
      <div class="summary-card">
        <p>Total Laporan</p>
        <h2>{{ laporan.length }}</h2>
      </div>

      <div class="summary-card">
        <p>Sedang Proses</p>
        <h2 class="orange">{{ prosesCount }}</h2>
      </div>

      <div class="summary-card">
        <p>Laporan Selesai</p>
        <h2 class="green">{{ selesaiCount }}</h2>
      </div>
    </div>

    <!-- TABLE -->
    <div class="table-wrapper">
      <table>
        <thead>
          <tr>
            <th class="col-no">No</th>
            <th>Judul Laporan</th>
            <th>Deskripsi</th>
            <th>Lokasi</th>
            <th class="col-status">Status</th>
          </tr>
        </thead>

        <tbody>
          <tr
            v-for="(item, index) in laporan"
            :key="item._id"
            class="row"
            @click="goDetail(item._id)"
            style="cursor: pointer"
          >
            <td class="col-no">{{ index + 1 }}</td>
            <td class="judul">{{ item.judul }}</td>
            <td class="desc">{{ item.deskripsi }}</td>
            <td>{{ item.lokasi }}</td>

            <td class="col-status">
              <span
                class="badge"
                :class="badgeClass(item.statusLaporan)"
              >
                {{ labelStatus(item.statusLaporan) }}
              </span>
            </td>
          </tr>

          <tr v-if="laporan.length === 0">
            <td colspan="5" class="empty">
              Belum ada laporan
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import api from "../services/api";

export default {
  name: "RiwayatLaporan",

  data() {
    return {
      laporan: [],
    };
  },

  computed: {
    prosesCount() {
      return this.laporan.filter(
        (l) => l.statusLaporan === "proses"
      ).length;
    },
    selesaiCount() {
      return this.laporan.filter(
        (l) => l.statusLaporan === "selesai"
      ).length;
    },
  },

  mounted() {
    this.fetchLaporan();
  },

  methods: {
    async fetchLaporan() {
      try {
        const res = await api.get("/api/laporan/me");
        this.laporan = res.data;
      } catch (err) {
        alert(
          err.response?.data?.message ||
          "Gagal mengambil data laporan"
        );
      }
    },

    goDetail(id) {
      if (!id) return;
      this.$router.push(`/riwayat/${id}`);
    },

    labelStatus(status) {
      if (status === "selesai") return "SELESAI";
      if (status === "ditolak") return "DITOLAK";
      return "PROSES";
    },

    badgeClass(status) {
      if (status === "selesai") return "selesai";
      if (status === "ditolak") return "ditolak";
      return "proses";
    },
  },
};
</script>

<style scoped>
.page {
  padding: 40px 60px;
}

.breadcrumb {
  font-size: 12px;
  color: #64748b;
  margin-bottom: 6px;
}

.title {
  font-size: 22px;
  font-weight: 700;
  margin-bottom: 20px;
}

/* SUMMARY */
.summary {
  display: flex;
  gap: 16px;
  margin-bottom: 20px;
}

.summary-card {
  background: #ffffff;
  padding: 16px 20px;
  border-radius: 12px;
  min-width: 180px;
}

.summary-card p {
  font-size: 13px;
  color: #64748b;
}

.summary-card h2 {
  margin-top: 6px;
  font-size: 24px;
  font-weight: 700;
}

.summary-card h2.orange {
  color: #f97316;
}

.summary-card h2.green {
  color: #22c55e;
}

/* TABLE */
.table-wrapper {
  background: #ffffff;
  border-radius: 14px;
  padding: 16px;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: #243e47;
  color: #fff;
}

th {
  padding: 14px 12px;
  font-size: 13px;
  font-weight: 600;
  text-align: left;
}

td {
  padding: 14px 12px;
  font-size: 13px;
}

.row:hover {
  background: #f2fdfb;
}

.col-no {
  width: 50px;
  text-align: center;
}

.col-status {
  width: 140px;
  text-align: center;
}

.judul {
  font-weight: 600;
}

.desc {
  font-size: 12px;
  color: #64748b;
}

/* BADGE */
.badge {
  padding: 4px 14px;
  border-radius: 20px;
  font-size: 11px;
  font-weight: 600;
}

.badge.proses {
  background: #facc15;
  color: #78350f;
}

.badge.ditolak {
  background: #ef4444;
  color: #fff;
}

.badge.selesai {
  background: #22c55e;
  color: #fff;
}

.empty {
  text-align: center;
  padding: 30px;
  color: #94a3b8;
}
</style>
