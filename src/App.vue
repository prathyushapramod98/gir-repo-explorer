<template>
  <div>
    <h1>GitHub Repository Explorer</h1>

    <!-- Input for GitHub Username -->
    <div>
      <label>GitHub Username:</label>
      <input
        type="text"
        v-model="username"
        placeholder="Enter GitHub Username"
      />
      <button @click="fetchRepos">Get Repos</button>
    </div>

    <!-- Error message -->
    <div v-if="errorMessage" class="error">{{ errorMessage }}</div>

    <!-- Loading indicator -->
    <div v-if="loading" class="loading">Loading...</div>

    <!-- Repository List -->
    <ul v-if="repos.length">
      <li v-for="repo in repos" :key="repo.id" @click="selectRepo(repo)">
        <strong>{{ repo.name }}</strong> (⭐ {{ repo.stargazers_count }})
      </li>
    </ul>

    <!-- Selected Repo Description -->
    <div v-if="selectedRepo">
      <h2>Description for {{ selectedRepo.name }}:</h2>
      <p>{{ selectedRepo.description || 'No description available' }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const username = ref(''); 
const repos = ref([]); 
const selectedRepo = ref(null); 
const errorMessage = ref(''); 
const loading = ref(false); 


const fetchRepos = async () => {
  if (!username.value) {
    errorMessage.value = 'Please enter a username';
    return;
  }

  errorMessage.value = ''; 
  
  loading.value = true;

  try {
    const response = await fetch(`https://api.github.com/users/${username.value}/repos`);

    if (!response.ok) {
      throw new Error('User not found');
    }

    const result = await response.json();
    repos.value = result;
    selectedRepo.value = null; 
  } catch (error) {
    errorMessage.value = error.message;
    repos.value = [];
  } finally {
    loading.value = false; 
  }
};

const selectRepo = (repo) => {
  selectedRepo.value = repo; 
  
};
</script>

<style scoped>
h1 {
  color: #274c71;
  text-align: center;
}


input {
  padding: 5px;
  margin-right: 10px;
}

button {
  padding: 6px 12px;
  background-color: #42b983;
  color: white;
  border: none;
  cursor: pointer;
  border-radius: 4px;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  padding: 10px;
  margin-bottom: 5px;
  border: 1px solid #ddd;
  cursor: pointer;
  background-color: #f9f9f9;
}

li:hover {
  background-color: #f0f0f0;
}

.error {
  color: red;
  margin-top: 10px;
}

.loading {
  font-weight: bold;
  color: #42b983;
}
</style>
