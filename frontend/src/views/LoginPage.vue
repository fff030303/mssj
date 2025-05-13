<template>
  <div class="login-container">
    <div class="login-card">
      <h1>AI多模型问答系统</h1>
      <h2>用户登录</h2>
      
      <div class="form-group">
        <label for="username">用户名</label>
        <input 
          type="text" 
          id="username" 
          v-model="username" 
          placeholder="请输入用户名"
          class="form-control"
        >
      </div>
      
      <div class="form-group">
        <label for="password">密码</label>
        <input 
          type="password" 
          id="password" 
          v-model="password" 
          placeholder="请输入密码"
          class="form-control"
        >
      </div>
      
      <div class="button-group">
        <button @click="login" class="btn btn-primary" :disabled="isLoading">
          {{ isLoading ? '登录中...' : '登录' }}
        </button>
        <button @click="goToRegister" class="btn btn-secondary">注册新账号</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'LoginPage',
  data() {
    return {
      username: '',
      password: '',
      isLoading: false
    }
  },
  methods: {
    login() {
      if (!this.username || !this.password) {
        alert('请输入用户名和密码');
        return;
      }
      
      this.isLoading = true;
      
      axios.post('/api/login', {
        username: this.username,
        password: this.password
      })
      .then(response => {
        if (response.data.success) {
          // 登录成功，保存用户信息
          localStorage.setItem('user_id', response.data.user_id);
          localStorage.setItem('username', response.data.username);
          
          // 跳转到问题页面，带上用户ID参数
          this.$router.push({
            name: 'question',
            query: { user_id: response.data.user_id }
          });
        }
      })
      .catch(error => {
        if (error.response && error.response.data) {
          alert(error.response.data.message || '登录失败');
        } else {
          alert('登录失败，请稍后再试');
        }
      })
      .finally(() => {
        this.isLoading = false;
      });
    },
    goToRegister() {
      this.$router.push({ name: 'register' });
    }
  }
}
</script>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background-color: #f5f5f5;
}

.login-card {
  width: 400px;
  padding: 30px;
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

h1 {
  text-align: center;
  margin-bottom: 10px;
  color: #333;
}

h2 {
  text-align: center;
  margin-bottom: 30px;
  color: #666;
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 8px;
  font-weight: bold;
  color: #555;
}

.form-control {
  width: 100%;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
}

.button-group {
  display: flex;
  gap: 15px;
  margin-top: 30px;
}

.btn {
  flex: 1;
  padding: 12px;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.btn-primary {
  background-color: #4CAF50;
  color: white;
}

.btn-primary:hover {
  background-color: #45a049;
}

.btn-secondary {
  background-color: #f1f1f1;
  color: #333;
}

.btn-secondary:hover {
  background-color: #e0e0e0;
}

.btn:disabled {
  opacity: 0.7;
  cursor: not-allowed;
}
</style>