<template>
  <div class="register-container">
    <div class="register-card">
      <h1>AI多模型问答系统</h1>
      <h2>用户注册</h2>
      
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
          placeholder="请输入密码（不少于6位）"
          class="form-control"
        >
        <small v-if="password.length > 0 && password.length < 6" class="text-danger">
          密码长度不能少于6位
        </small>
      </div>
      
      <div class="button-group">
        <button @click="register" class="btn btn-primary" :disabled="isLoading || !isValidForm">
          {{ isLoading ? '注册中...' : '注册' }}
        </button>
        <button @click="goToLogin" class="btn btn-secondary">返回登录</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'RegisterPage',
  data() {
    return {
      username: '',
      password: '',
      isLoading: false
    }
  },
  computed: {
    isValidForm() {
      return this.username.length > 0 && this.password.length >= 6;
    }
  },
  methods: {
    register() {
      if (!this.username) {
        alert('请输入用户名');
        return;
      }
      
      if (this.password.length < 6) {
        alert('密码需要不少于6位！');
        return;
      }
      
      this.isLoading = true;
      
      axios.post('/api/register', {
        username: this.username,
        password: this.password
      })
      .then(response => {
        if (response.data.success) {
          alert('注册成功！');
          // 1秒后跳转到登录页面
          setTimeout(() => {
            this.$router.push({ name: 'login' });
          }, 1000);
        }
      })
      .catch(error => {
        if (error.response && error.response.data) {
          alert(error.response.data.message || '注册失败');
        } else {
          alert('注册失败，请稍后再试');
        }
      })
      .finally(() => {
        this.isLoading = false;
      });
    },
    goToLogin() {
      this.$router.push({ name: 'login' });
    }
  }
}
</script>

<style scoped>
.register-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background-color: #f5f5f5;
}

.register-card {
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

.text-danger {
  color: #dc3545;
  font-size: 14px;
  margin-top: 5px;
  display: block;
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

.btn-primary:hover:not(:disabled) {
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