<template>
  <div>
    <h1>用户管理</h1>
    
    <!-- 创建/更新用户的表单 -->
    <div>
      <input v-model="userForm.name" placeholder="姓名" />
      <input v-model="userForm.email" placeholder="邮箱" />
      <button @click="isEditing ? updateUser() : createUser()">
        {{ isEditing ? '更新用户' : '创建用户' }}
      </button>
      <button v-if="isEditing" @click="cancelEdit()">取消</button>
    </div>

    <!-- 搜索用户 -->
    <div>
      <input v-model="searchName" placeholder="输入姓名搜索" />
      <button @click="searchUsers()">搜索</button>
    </div>

    <!-- 用户列表及分页显示 -->
    <div>
      <ul>
        <li v-for="user in users" :key="user.id">
          {{ user.name }} ({{ user.email }})
          <button @click="editUser(user)">编辑</button>
          <button @click="deleteUser(user.id)">删除</button>
        </li>
      </ul>
    </div>
    
    <!-- 分页控制 -->
    <div>
      <button @click="changePage(currentPage - 1)" :disabled="currentPage === 1">上一页</button>
      <button @click="changePage(currentPage + 1)" :disabled="currentPage === totalPages">下一页</button>
    </div>
    
    <p>当前第 {{ currentPage }} 页，共 {{ totalPages }} 页</p>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      users: [],
      userForm: {
        id: null,
        name: '',
        email: '',
      },
      searchName: '',
      isEditing: false,
      currentPage: 1,
      pageSize: 5,
      totalPages: 1,
    };
  },
  created() {
    this.fetchUsers();
  },
  methods: {
    async fetchUsers() {
      const response = await axios.get(`http://localhost:8081/list/${this.currentPage}/${this.pageSize}`);
      this.users = response.data.users;
      this.totalPages = Math.ceil(response.data.total / this.pageSize);
    },
    async createUser() {
      await axios.post('http://localhost:8081/create', this.userForm);
      this.resetForm();
      this.fetchUsers();
    },
    async updateUser() {
      await axios.put('http://localhost:8081/update', this.userForm);
      this.resetForm();
      this.fetchUsers();
    },
    async deleteUser(id) {
      await axios.delete(`http://localhost:8081/delete`, { params: { id } });
      this.fetchUsers();
    },
    async searchUsers() {
      const response = await axios.get(`http://localhost:8081/search/${this.currentPage}/${this.pageSize}`, {
        params: { name: this.searchName }
      });
      this.users = response.data.users;
      this.totalPages = Math.ceil(response.data.total / this.pageSize);
    },
    editUser(user) {
      this.userForm = { ...user };
      this.isEditing = true;
    },
    cancelEdit() {
      this.resetForm();
    },
    resetForm() {
      this.userForm = {
        id: null,
        name: '',
        email: '',
      };
      this.isEditing = false;
    },
    changePage(page) {
      if (page >= 1 && page <= this.totalPages) {
        this.currentPage = page;
        if (this.searchName) {
          this.searchUsers();
        } else {
          this.fetchUsers();
        }
      }
    },
  },
};
</script>

<style scoped>
.user-management {
  max-width: 600px;
  margin: 0 auto;
  font-family: Arial, sans-serif;
  color: #333;
}

h1 {
  text-align: center;
  color: #007bff;
  margin-bottom: 20px;
}

.form-container, .search-container {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}

input {
  flex: 1;
  padding: 8px;
  margin-right: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  padding: 8px 12px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  background-color: #007bff;
  color: #fff;
}

button:hover {
  background-color: #0056b3;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.user-list {
  background-color: #f9f9f9;
  border-radius: 4px;
  padding: 15px;
}

.user-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 0;
  border-bottom: 1px solid #eee;
}

.user-item:last-child {
  border-bottom: none;
}

.user-item span {
  margin-right: 10px;
}

ul {
  list-style-type: none;
  padding: 0;
}

.action-buttons {
  display: flex;
  gap: 15px;
}

.delete-button {
  background-color: #dc3545;
}

.delete-button:hover {
  background-color: #c82333;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}

.pagination p {
  margin: 0 15px;
  color: #555;
}
</style>
