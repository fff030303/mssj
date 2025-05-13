<template>
  <div class="question-container">
    <div class="header">
      <h1>AI多模型问答系统</h1>
      <div class="user-info">
        <span>欢迎，{{ username }}</span>
        <button @click="logout" class="logout-btn">退出登录</button>
      </div>
    </div>
    
    <div class="question-form">
      <textarea 
        v-model="question" 
        placeholder="请输入您的问题..." 
        rows="5"
        class="question-input"
      ></textarea>
      <button @click="submitQuestion" class="submit-btn" :disabled="isSubmitting">
        {{ isSubmitting ? '提交中...' : '提交问题' }}
      </button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'QuestionPage',
  data() {
    return {
      question: '',
      isSubmitting: false,
      userId: null,
      username: '用户'
    }
  },
  created() {
    // 检查登录状态
    this.checkLoginStatus();
    
    // 获取URL中的用户ID
    const urlUserId = this.$route.query.user_id;
    if (urlUserId) {
      this.userId = urlUserId;
    }
    
    // 获取本地存储的用户名
    const storedUsername = localStorage.getItem('username');
    if (storedUsername) {
      this.username = storedUsername;
    }
  },
  methods: {
    checkLoginStatus() {
      const userId = localStorage.getItem('user_id');
      if (!userId) {
        // 未登录，跳转到登录页面
        this.$router.push({ name: 'login' });
      } else {
        this.userId = userId;
      }
    },
    submitQuestion() {
      if (!this.question.trim()) {
        alert('请输入问题');
        return;
      }
      
      this.isSubmitting = true;
      
      // 调用后端API，注意接口路径和字段名
      axios.post('/api/chat', { 
        query: this.question,
        user_id: this.userId  // 添加用户ID
      })
        .then(response => {
          this.$router.push({
            name: 'answer',
            query: { 
              spark_answer: response.data.spark_answer,
              qianfan_answer: response.data.qianfan_answer,
              free_qwq_answer: response.data.free_qwq_answer,
              question: this.question,
              user_id: this.userId  // 传递用户ID
            }
          });
        })
        .catch(error => {
          console.error('提交问题出错:', error);
          alert('提交问题时发生错误，请稍后再试');
        })
        .finally(() => {
          this.isSubmitting = false;
        });
    },
    logout() {
      // 清除本地存储的用户信息
      localStorage.removeItem('user_id');
      localStorage.removeItem('username');
      
      // 跳转到登录页面
      this.$router.push({ name: 'login' });
    }
  }
}
</script>

<style scoped>
.question-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 15px;
}

.logout-btn {
  padding: 8px 12px;
  background-color: #f44336;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}

.logout-btn:hover {
  background-color: #d32f2f;
}

.question-form {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.question-input {
  width: 100%;
  padding: 15px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
  resize: vertical;
}

.submit-btn {
  padding: 12px 24px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  align-self: flex-end;
}

.submit-btn:hover:not(:disabled) {
  background-color: #45a049;
}

.submit-btn:disabled {
  opacity: 0.7;
  cursor: not-allowed;
}
</style>