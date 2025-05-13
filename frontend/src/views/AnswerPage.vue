<template>
  <div class="answer-container">
    <div class="header">
      <h1>AI多模型问答系统</h1>
      <div class="user-info">
        <span>欢迎，{{ username }}</span>
        <button @click="logout" class="logout-btn">退出登录</button>
      </div>
    </div>
    
    <div class="content">
      <h2>您的问题：</h2>
      <div class="question-box">{{ question }}</div>
      
      <div class="answers-grid">
        <div class="answer-card">
          <h2>讯飞星火回答：</h2>
          <div class="answer-box">{{ sparkAnswer }}</div>
        </div>
        
        <div class="answer-card">
          <h2>百度千帆回答：</h2>
          <div class="answer-box">{{ qianfanAnswer }}</div>
        </div>
        
        <div class="answer-card">
          <h2>FREE-QWQ回答：</h2>
          <div class="answer-box">{{ freeQwqAnswer }}</div>
        </div>
      </div>
      
      <button @click="goBack" class="back-btn">返回提问</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'AnswerPage',
  data() {
    return {
      question: this.$route.query.question || '未提供问题',
      sparkAnswer: this.$route.query.spark_answer || '未获取到讯飞星火回答',
      qianfanAnswer: this.$route.query.qianfan_answer || '未获取到百度千帆回答',
      freeQwqAnswer: this.$route.query.free_qwq_answer || '未获取到FREE-QWQ回答',
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
    goBack() {
      this.$router.push({ 
        name: 'question',
        query: { user_id: this.userId }
      });
    },
    logout() {
      // 清除本地存储的用户信息
      localStorage.removeItem('user_id');
      localStorage.removeItem('username');
      
      // 跳转到登录页面
      this.$router.push({ name: 'login' });
    }
  },
  beforeMount() {
    if (!this.$route.query.spark_answer && !this.$route.query.qianfan_answer && !this.$route.query.free_qwq_answer) {
      this.$router.push({ 
        name: 'question',
        query: { user_id: this.userId }
      });
    }
  }
}
</script>

<style scoped>
.answer-container {
  max-width: 1200px;
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

.content {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.question-box, .answer-box {
  padding: 15px;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: #f9f9f9;
  white-space: pre-wrap;
  overflow-wrap: break-word;
}

.answers-grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 20px;
}

@media (max-width: 1024px) {
  .answers-grid {
    grid-template-columns: 1fr;
  }
}

.answer-card {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.back-btn {
  padding: 12px 24px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s;
  margin-top: 20px;
  align-self: flex-start;
}

.back-btn:hover {
  background-color: #45a049;
}

h1, h2 {
  margin-bottom: 10px;
  color: #333;
}
</style>