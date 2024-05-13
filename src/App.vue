<template>
    <div class="container">
      <header class="header">
        <nav class="navbar">
          <router-link to="/" class="nav-link active">Todo App</router-link>
          <router-link to="/posts" class="nav-link">Posts</router-link>
        </nav>
      </header>
      <div class="posts" id="posts">
        <h1>Postingan User</h1>
        <h2>{{ selectedUserName }}</h2>
        <select v-model="selectedUser" class="selectModel" @change="fetchPosts">
          <option value="">Pilih Pengguna</option>
          <option v-for="user in users" :value="user.id" :key="user.id">{{ user.name }}</option>
        </select>
  
        <div v-if="isLoading" class="loading">
          <span class="loading-message">Memuat...</span>
          <div class="loader"></div>
        </div>
  
        <ul v-if="!isLoading" class="post-list">
          <li v-for="post in posts" :key="post.id" class="post-item">
            <h3>{{ post.title }}</h3>
            <p>{{ post.body }}</p>
          </li>
        </ul>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted, watch } from 'vue';
  
  const isLoading = ref(false);
  const selectedUser = ref(null);
  const selectedUserName = ref('');
  const users = ref([]);
  const posts = ref([]);
  
  const fetchUser = async () => {
    try {
      isLoading.value = true;
      const response = await fetch('https://jsonplaceholder.typicode.com/users');
      const data = await response.json();
      users.value = data;
    } catch (error) {
      console.error('Error fetching users:', error);
    } finally {
      isLoading.value = false;
    }
  };
  
  const fetchPosts = async () => {
    if (!selectedUser.value) return;
    isLoading.value = true;
    try {
      const response = await fetch(`https://jsonplaceholder.typicode.com/posts?userId=${selectedUser.value}`);
      const data = await response.json();
      posts.value = data;
    } catch (error) {
      console.error('Error fetching posts:', error);
    } finally {
      isLoading.value = false;
    }
  };
  
  onMounted(() => {
    fetchUser();
  });
  
  watch(selectedUser, () => {
    posts.value = [];
    fetchPosts();
    const selectedUserObject = users.value.find(user => user.id === selectedUser.value);
    selectedUserName.value = selectedUserObject ? selectedUserObject.name : '';
  }, { immediate: true });
  </script>
  
  <style scoped>
  /* Global styles */
  body {
    font-family: 'Arial', sans-serif;
    background-color: #f4f4f4;
    margin: 0;
    padding: 0;
  }
  
  /* Header styles */
  .header {
    background-color: #1976d2;
    color: #fff;
    padding: 1rem;
  }
  
  .navbar {
    display: flex;
    justify-content: center;
    align-items: center;
  }
  
  .nav-link {
    color: #fff;
    text-decoration: none;
    font-size: 1.2rem;
    margin: 0 10px;
    transition: color 0.3s;
  }
  
  .nav-link:hover {
    color: #f9a825;
  }
  
  /* Container styles */
  .container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 20px;
  }
  
  .posts {
    background-color: #fff;
    border-radius: 10px;
    padding: 20px;
    margin-top: 20px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  }
  
  /* Select styles */
  .selectModel {
    width: 100%;
    padding: 10px;
    font-size: 16px;
    border: 1px solid #ccc;
    border-radius: 5px;
    background-color: #f9f9f9;
    cursor: pointer;
    transition: border-color 0.3s ease;
  }
  
  .selectModel:focus {
    outline: none;
    border-color: #1976d2;
  }
  
  /* Loading styles */
  .loading {
    text-align: center;
  }
  
  .loader {
    border: 6px solid #f3f3f3;
    border-top: 6px solid #1976d2;
    border-radius: 50%;
    width: 30px;
    height: 30px;
    animation: spin 1s linear infinite;
    margin: 0 auto;
  }
  
  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }
  
  .loading-message {
    font-size: 18px;
    color: #555;
    margin-bottom: 10px;
  }
  
  /* Post list styles */
  .post-list {
    list-style-type: none;
    padding: 0;
  }
  
  .post-item {
    background-color: #f9f9f9;
    border-radius: 5px;
    padding: 10px;
    margin-bottom: 10px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    transition: background-color 0.3s ease;
  }
  
  .post-item:hover {
    background-color: #e0e0e0;
  }
  
  .post-item h3 {
    margin-bottom: 5px;
    color: #333;
  }
  
  .post-item p {
    color: #555;
  }
  </style>
  