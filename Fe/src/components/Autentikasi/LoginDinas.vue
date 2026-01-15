<template>
  <div class="login-wrapper">
    <div class="login-box">
      <h2>Login Dinas</h2>

      <form @submit.prevent="login">
        <div class="form-group">
          <label>NIP PEGAWAI</label>
          <input
            type="text"
            v-model="nip"
            placeholder="19801234"
            required
          />
        </div>

        <div class="form-group">
          <label>KATA SANDI</label>
          <input
            type="password"
            v-model="password"
            placeholder="••••••••"
            required
          />
        </div>

        <button class="btn-login">LOGIN</button>
      </form>
    </div>
  </div>
</template>

<script>
import api from "@/services/api";

export default {
  name: "LoginDinas",
  data() {
    return {
      nip: "",
      password: "",
    };
  },
  methods: {
    async login() {
      try {
        const res = await api.post("/api/login", {
          identifier: this.nip,
          password: this.password,
          role: "dinas",
        });

        localStorage.setItem("token", res.data.token);
        localStorage.setItem("user", JSON.stringify(res.data.user));

        window.dispatchEvent(new Event("auth-change"));

        this.$router.push("/dinas/dashboard");
      } catch (err) {
        alert(err.response?.data?.message || "Login dinas gagal");
      }
    },
  },
};
</script>


<style scoped>
/* ===== LAYOUT ===== */
.login-wrapper {
  display: flex;
  min-height: 100vh;
  font-family: Inter, system-ui, sans-serif;
}

/* ===== LEFT ===== */
.left {
  flex: 1;
  background: #294b57;
  color: #fff;
  padding: 80px 60px;
  display: flex;
  align-items: center;
}

.brand h1 {
  font-size: 40px;
  font-weight: 700;
}

.brand span {
  color: #36c6b3;
}

.brand p {
  margin-top: 14px;
  font-size: 14px;
  color: #cbd5e1;
  max-width: 380px;
  line-height: 1.6;
}

.line {
  width: 48px;
  height: 4px;
  background: #36c6b3;
  margin-top: 20px;
  border-radius: 4px;
}

/* ===== RIGHT ===== */
.right {
  flex: 1;
  background: #ffffff;
  display: flex;
  align-items: center;
  justify-content: center;
}

.login-box {
  width: 360px;
}

.login-box h2 {
  font-size: 26px;
  font-weight: 700;
  color: #0f172a;
}

.subtitle {
  margin: 10px 0 28px;
  font-size: 14px;
  color: #64748b;
}

/* ===== FORM ===== */
.form-group {
  margin-bottom: 18px;
}

.form-group label {
  font-size: 12px;
  color: #475569;
  font-weight: 600;
}

.form-group input {
  width: 100%;
  padding: 12px;
  border-radius: 10px;
  border: 1px solid #e5e7eb;
  margin-top: 6px;
  font-size: 14px;
}

.form-group input:focus {
  outline: none;
  border-color: #36c6b3;
}

/* ===== BUTTON ===== */
.btn-login {
  width: 100%;
  margin-top: 12px;
  padding: 14px;
  border-radius: 12px;
  border: none;
  background: #294b57;
  color: #fff;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.25s ease;
}

.btn-login:hover {
  background: #1f3a44;
}

/* ===== FOOTER ===== */
.help {
  margin-top: 20px;
  font-size: 12px;
  color: #94a3b8;
  text-align: center;
}
</style>
