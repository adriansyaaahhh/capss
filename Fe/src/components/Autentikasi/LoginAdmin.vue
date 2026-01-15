<template>
  <div class="admin-login">
    <div class="form-box">
      <h2>Login Admin</h2>

      <form @submit.prevent="login">
        <div class="form-group">
          <label>USERNAME ADMIN</label>
          <input
            type="text"
            v-model="username"
            placeholder="admin01"
            required
          />
        </div>

        <div class="form-group">
          <label>KATA SANDI</label>
          <input
            type="password"
            v-model="password"
            placeholder="********"
            required
          />
        </div>

        <button type="submit">LOGIN</button>
      </form>
    </div>
  </div>
</template>

<script>
import api from "@/services/api";

export default {
  name: "LoginAdmin",
  data() {
    return {
      username: "",
      password: "",
    };
  },
  methods: {
    async login() {
      try {
        const res = await api.post("/api/login", {
          identifier: this.username,
          password: this.password,
          role: "admin",
        });

        localStorage.setItem("token", res.data.token);
        localStorage.setItem("user", JSON.stringify(res.data.user));

        window.dispatchEvent(new Event("auth-change"));

        this.$router.push("/admin/dashboard");
      } catch (err) {
        alert(err.response?.data?.message || "Login admin gagal");
      }
    },
  },
};
</script>


<style scoped>
.admin-login {
  display: flex;
  min-height: 100vh;
  font-family: "Inter", sans-serif;
}

/* LEFT */
.left {
  flex: 1;
  background: #294b57;
  display: flex;
  align-items: center;
  padding: 80px;
  color: #fff;
}

.branding h1 {
  font-size: 42px;
  margin-bottom: 14px;
}

.portal {
  color: #36c6b3;
}

.admin {
  color: #ffffff;
}

.branding p {
  font-size: 14px;
  max-width: 420px;
  line-height: 1.6;
  color: #d1e5e3;
}

.line {
  width: 60px;
  height: 4px;
  background: #36c6b3;
  margin-top: 20px;
}

/* RIGHT */
.right {
  flex: 1;
  background: #ffffff;
  display: flex;
  align-items: center;
  justify-content: center;
}

.form-box {
  width: 360px;
}

h2 {
  margin-bottom: 6px;
  font-size: 22px;
  color: #1f2937;
}

.subtitle {
  font-size: 13px;
  color: #6b7280;
  margin-bottom: 28px;
}

.form-group {
  margin-bottom: 18px;
}

label {
  font-size: 11px;
  font-weight: 600;
  color: #6b7280;
  margin-bottom: 6px;
  display: block;
}

input {
  width: 100%;
  padding: 10px 12px;
  border-radius: 8px;
  border: 1px solid #e5e7eb;
  background: #f9fafb;
}

button {
  width: 100%;
  padding: 12px;
  margin-top: 10px;
  border-radius: 8px;
  border: none;
  background: #294b57;
  color: #fff;
  font-weight: 600;
  cursor: pointer;
}

.help {
  display: block;
  text-align: center;
  margin-top: 18px;
  font-size: 12px;
  color: #6b7280;
}
</style>
