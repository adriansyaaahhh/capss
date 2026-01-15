<template>
  <div class="page">
    <div class="breadcrumb">
      Tugas Saya &gt; Update Pengerjaan
    </div>
<div class="page-header">
  <button class="btn-back" @click="$router.push('/dinas/dashboard')">
    <span class="icon">←</span>
    <span>Kembali ke Dashboard</span>
  </button>

  <h1>Laporan Penyelesaian Tugas</h1>
</div>



    <div class="card">
      <!-- 1. REFERENSI -->
      <div class="section">
        <h3>1. Referensi Masalah</h3>

        <div class="ref-box">
          <div class="ref-photo">
            <img
              v-if="task.foto"
              :src="task.foto"
              alt="Foto Awal"
            />
            <div v-else class="no-photo">
              Foto Awal
            </div>
          </div>

          <div class="ref-info">
            <p><b>Lokasi:</b> {{ task.lokasi }}</p>
            <p><b>Kategori:</b> {{ task.judul }}</p>
            <p class="status">
              Status:
              <span class="working">Sedang Dikerjakan</span>
            </p>
          </div>
        </div>
      </div>

      <!-- 2. BUKTI -->
      <div class="section">
        <h3>2. Bukti Pengerjaan (Wajib)</h3>

        <label class="upload-box">
          <input type="file" accept="image/*" @change="handleUpload" hidden />
          <div v-if="previewAfter" class="preview-after">
            <img :src="previewAfter" />
          </div>
          <div v-else class="upload-placeholder">
            + Upload Foto Selesai
            <small>Pastikan foto terang dan jelas</small>
          </div>
        </label>
      </div>

      <!-- 3. CATATAN -->
      <div class="section">
        <h3>3. Catatan Petugas</h3>

        <textarea
          v-model="catatanPetugas"
          placeholder="Perbaikan telah dilakukan dengan menambal aspal menggunakan hotmix..."
          rows="4"
        ></textarea>
      </div>

      <!-- 4. KONFIRMASI -->
      <div class="section">
        <h3>4. Konfirmasi Status</h3>

        <select v-model="statusAkhir">
          <option value="selesai">SELESAI (Tutup Tiket)</option>
        </select>
      </div>

      <!-- SUBMIT -->
      <button class="submit" @click="kirimLaporan">
        KIRIM LAPORAN SELESAI
      </button>
    </div>
  </div>
</template>

<script>
  
export default {
  name: "DinasSelesaiTugas",
  data() {
    return {
      task: {},
      previewAfter: "",
      fotoAfter: "",
      catatanPetugas: "",
      statusAkhir: "selesai",
    };
  },


async mounted() {
  const id = this.$route.params.id;

  try {
    const res = await api.get(`/dinas/laporan/${id}`);
    this.task = res.data;

    // 🔥 INI KUNCI FOTO AWAL
    if (this.task.foto) {
      this.task.foto =
        "http://localhost:3000/uploads/laporan/" + this.task.foto;
    }
  } catch (err) {
    console.error(err);
    alert("Gagal mengambil data laporan");
  }
},

  methods: {
    handleUpload(e) {
      const file = e.target.files[0];
      if (!file) return;

      const reader = new FileReader();
      reader.onload = () => {
        this.previewAfter = reader.result;
        this.fotoAfter = reader.result;
      };
      reader.readAsDataURL(file);
    },

kirimLaporan() {
  if (!this.fotoAfter || !this.catatanPetugas) {
    alert("Foto dan catatan wajib diisi");
    return;
  }

  // ================= DINAS TASK =================
  const tasks =
    JSON.parse(localStorage.getItem("dinas_tasks")) || [];

  const idx = tasks.findIndex(t => t.id === this.task.id);
  if (idx === -1) return;

  tasks[idx].status = "selesai";
  tasks[idx].fotoAfter = this.fotoAfter;
  tasks[idx].catatanPetugas = this.catatanPetugas;
  tasks[idx].tanggalSelesai = new Date().toLocaleDateString("id-ID");

  localStorage.setItem(
    "dinas_tasks",
    JSON.stringify(tasks)
  );

  // ================= RIWAYAT SELESAI =================
  const riwayat =
    JSON.parse(localStorage.getItem("riwayat_selesai")) || [];

  riwayat.unshift({
    id: this.task.id,
    judul: this.task.judul,
    lokasi: this.task.lokasi,
    fotoBefore: this.task.foto,
    fotoAfter: this.fotoAfter,
    adminNote: this.task.adminNote || "-",
    catatanPetugas: this.catatanPetugas,
    tanggalSelesai: new Date().toLocaleDateString("id-ID"),
    dinas: "Dinas PU",
  });

  localStorage.setItem(
    "riwayat_selesai",
    JSON.stringify(riwayat)
  );

  // ================= UPDATE STATUS USER =================
  const laporan =
    JSON.parse(localStorage.getItem("laporan")) || [];

  const lapIdx = laporan.findIndex(
    l => l.id === this.task.id
  );

  if (lapIdx !== -1) {
    laporan[lapIdx].status = "selesai";
    laporan[lapIdx].tanggalSelesai =
      new Date().toLocaleDateString("id-ID");

    localStorage.setItem(
      "laporan",
      JSON.stringify(laporan)
    );
  }

  alert("✅ Laporan berhasil diselesaikan");

  // 🔥 INI KUNCI UTAMA (JANGAN SALAH LAGI)
  this.$router.replace("/dinas/dashboard");
}


  },
};
</script>

<style scoped>
.page {
  max-width: 1000px;
  margin: 0 auto;
}

.breadcrumb {
  font-size: 13px;
  color: #64748b;
  margin-bottom: 8px;
}

.title {
  margin-bottom: 20px;
}

.card {
  background: #fff;
  border-radius: 16px;
  padding: 24px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.05);
}

.section {
  margin-bottom: 22px;
}

.section h3 {
  font-size: 14px;
  margin-bottom: 12px;
}

/* REFERENSI */
.ref-box {
  display: flex;
  gap: 16px;
}

.ref-photo {
  width: 120px;
  height: 90px;
  background: #e5e7eb;
  border-radius: 10px;
  overflow: hidden;
}

.ref-photo img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.ref-info p {
  font-size: 14px;
}

.working {
  color: #f59e0b;
  font-weight: 600;
}

/* UPLOAD */
.upload-box {
  border: 2px dashed #94a3b8;
  border-radius: 14px;
  padding: 30px;
  display: block;
  text-align: center;
  cursor: pointer;
}

.upload-placeholder small {
  display: block;
  margin-top: 6px;
  color: #64748b;
}

.preview-after img {
  width: 100%;
  border-radius: 12px;
}

/* TEXTAREA */
textarea {
  width: 100%;
  border-radius: 10px;
  padding: 12px;
  border: 1px solid #e5e7eb;
}

/* SELECT */
select {
  padding: 10px;
  border-radius: 10px;
  border: 1px solid #e5e7eb;
}

/* SUBMIT */
.submit {
  width: 100%;
  background: #294b57;
  color: #fff;
  padding: 14px;
  border-radius: 12px;
  font-weight: 700;
  border: none;
  cursor: pointer;
}
.back {
  background: none;
  border: none;
  color: #475569;
  font-weight: 600;
  cursor: pointer;
  margin-bottom: 12px;
}

.back:hover {
  text-decoration: underline;
}
.page-header {
  margin-bottom: 24px;
}

/* === BACK BUTTON === */
.btn-back {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 10px 16px;
  border-radius: 999px;
  background: #f1f5f9;
  border: 1px solid #e2e8f0;
  color: #334155;
  font-weight: 600;
  font-size: 14px;
  cursor: pointer;
  transition: all 0.25s ease;
  .btn-back {
  background: transparent;
  border: none;
  padding: 0;
  font-size: 14px;
}
}

.btn-back .icon {
  font-size: 16px;
  transition: transform 0.25s ease;
}

/* HOVER EFFECT */
.btn-back:hover {
  background: #e2e8f0;
  color: #0f172a;
   text-decoration: underline;
}

.btn-back:hover .icon {
  transform: translateX(-4px);
}

/* ACTIVE (klik) */
.btn-back:active {
  transform: scale(0.97);
}


</style>
