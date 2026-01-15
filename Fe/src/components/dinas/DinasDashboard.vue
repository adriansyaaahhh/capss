<template>
  <div class="page">
    <h1 class="title">Tindak Lanjut Lapangan</h1>

    <!-- LIST LAPORAN -->
    <div
      v-for="task in tasks"
      :key="task.id"
      class="task-card"
    >
      <!-- ================= MODE NORMAL ================= -->
      <div v-if="activeTaskId !== task.id">
        <div class="top">
          <span class="badge tinggi">
            PRIORITAS {{ task.prioritas?.toUpperCase() }}
          </span>
          <span class="task-id">ID: #LP-{{ task.id }}</span>
        </div>

        <h3>{{ task.judul }}</h3>
        <p class="lokasi">{{ task.lokasi }}</p>

        <p class="admin-note">
          <strong>Catatan Admin</strong><br />
          {{ task.admin_note || "-" }}
        </p>

        <div class="actions">
          <button class="btn outline" @click="openPreview(task)">
            Preview
          </button>

          <button class="btn primary" @click="kerjakan(task)">
            Kerjakan
          </button>
        </div>
      </div>

      <!-- ================= MODE KERJAKAN ================= -->
      <div v-else class="kerjakan-box">
        <h3>{{ task.judul }}</h3>
        <p>{{ task.lokasi }}</p>

        <h4>Catatan Admin</h4>
        <p>{{ task.admin_note || "-" }}</p>

<div class="upload-box">
  <div>
    <h4>Update Progress & Upload Bukti</h4>
    <p>
      Upload foto <b>After</b> perbaikan untuk menyelesaikan laporan.
    </p>
  </div>

<button
  class="btn primary"
  @click="$router.push(`/dinas/selesai/${task.id}`)"
>
  Selesai & Upload
</button>

</div>

      </div>
    </div>

    <!-- ================= PREVIEW MODAL ================= -->
    <div v-if="showPreview" class="modal-overlay">
      <div class="modal">
        <div class="modal-header">
          <h3>Detail Laporan</h3>
          <span class="close" @click="closePreview">×</span>
        </div>

        <div class="modal-body" v-if="selectedTask">
          <div class="photo">
            <img
              v-if="selectedTask.foto"
              :src="`http://localhost:3000/uploads/laporan/${selectedTask.foto}`"
            />
            <div v-else class="no-photo">Tidak ada foto</div>
          </div>

          <div class="info">
            <p><b>Judul:</b> {{ selectedTask.judul }}</p>
            <p><b>Lokasi:</b> {{ selectedTask.lokasi }}</p>
            <p><b>Deskripsi:</b> {{ selectedTask.deskripsi }}</p>
            <p><b>Catatan Admin:</b> {{ selectedTask.admin_note || "-" }}</p>
          </div>
        </div>

        <div class="modal-footer">
          <button class="btn outline" @click="closePreview">
            Tutup
          </button>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import api from "../../services/api";

export default {
  name: "DinasDashboard",

  data() {
    return {
      tasks: [],
      activeTaskId: null,
      showPreview: false,
      selectedTask: null,
      fileAfter: null,
    };
  },

  async mounted() {
    await this.fetchTasks();
  },

  methods: {
    async fetchTasks() {
      try {
        const res = await api.get("/dinas/laporan");
        this.tasks = res.data;
      } catch (err) {
        alert("Gagal mengambil tugas dinas");
      }
    },

    openPreview(task) {
      this.selectedTask = task;
      this.showPreview = true;
    },

    closePreview() {
      this.showPreview = false;
      this.selectedTask = null;
    },

    async kerjakan(task) {
      try {
        await api.put(`/dinas/laporan/${task.id}/kerjakan`);
        this.activeTaskId = task.id;
      } catch (err) {
        alert("Gagal memulai pengerjaan");
      }
    },

    batal() {
      this.activeTaskId = null;
      this.fileAfter = null;
    },

    handleFile(e) {
      this.fileAfter = e.target.files[0];
    },

    async selesai(id) {
      if (!confirm("Tandai laporan sebagai selesai?")) return;

      try {
        await api.put(`/dinas/laporan/${id}/selesai`);
        alert("Laporan selesai");
        this.activeTaskId = null;
        this.fetchTasks();
      } catch (err) {
        alert("Gagal menyelesaikan laporan");
      }
    },
  },
};
</script>

<style scoped>
/* === CSS KAMU TIDAK DIUBAH === */
.page {
  max-width: 1000px;
  margin: 0 auto;
}
.title {
  margin-bottom: 24px;
}
.task-card {
  background: #fff;
  border-radius: 16px;
  padding: 20px 24px;
  margin-bottom: 18px;
  box-shadow: 0 10px 25px rgba(0,0,0,0.06);
}
/* ===== UPLOAD BOX (PERSIS REFERENSI) ===== */
.upload-box {
  margin-top: 16px;
  background: #f8fbff;
  border: 2px dashed #c7dbff;
  border-radius: 14px;
  padding: 20px 22px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.upload-box h4 {
  font-size: 16px;
  font-weight: 700;
  margin-bottom: 6px;
}

.upload-box p {
  font-size: 14px;
  color: #64748b;
  margin: 0;
}

.top {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.task-id {
  font-size: 12px;
  color: #64748b;
}
.lokasi {
  font-size: 14px;
  color: #64748b;
}
.badge {
  font-size: 11px;
  font-weight: 700;
  padding: 4px 12px;
  border-radius: 999px;
}
.badge.tinggi {
  background: #fee2e2;
  color: #991b1b;
}
.actions {
  display: flex;
  gap: 10px;
}
.btn {
  padding: 8px 18px;
  border-radius: 10px;
  font-weight: 600;
  cursor: pointer;
  border: none;
}
.btn.primary {
  background: #10b981;
  color: #fff;
}
.btn.outline {
  background: #fff;
  border: 1px solid #cbd5e1;
  color: #334155;
}

/* MODAL */
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.45);
  display: flex;
  align-items: center;
  justify-content: center;
}
.modal {
  background: #fff;
  width: 760px;
  border-radius: 18px;
}
.modal-header {
  padding: 16px 24px;
  display: flex;
  justify-content: space-between;
  border-bottom: 1px solid #e5e7eb;
}
.close {
  font-size: 22px;
  cursor: pointer;
}
.modal-body {
  display: grid;
  grid-template-columns: 1fr 1.2fr;
  gap: 20px;
  padding: 20px 24px;
}
.photo {
  background: #f1f5f9;
  border-radius: 14px;
  height: 260px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.photo img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
.no-photo {
  color: #94a3b8;
}
.modal-footer {
  padding: 16px 24px;
  text-align: right;
}
</style>
