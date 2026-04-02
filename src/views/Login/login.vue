<script setup>
import { onMounted } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()

onMounted(() => {
  // 重定向到新的登录页面
  router.push('/login')
})
</script>

<template>
  <div class="login-container">
    <div class="login-box">
      <div class="logo">
        <img src="../../assets/logo.png" alt="理程学院" />
      </div>
      <h2 class="login-title">正在跳转到登录页面...</h2>
    </div>
  </div>
</template>

<style scoped>
.login-container {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.login-box {
  width: 400px;
  padding: 40px;
  background: white;
  border-radius: 10px;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.logo {
  margin-bottom: 30px;
}

.logo img {
  max-width: 240px;
  max-height: 240px;
  width: auto;
  height: auto;
  margin-bottom: 20px;
}

.login-title {
  font-size: 20px;
  font-weight: bold;
  color: #333;
  margin: 0;
}
</style>