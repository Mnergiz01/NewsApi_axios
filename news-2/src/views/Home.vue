<template>
  <div class="max-w-4xl mx-auto p-6">
    <!-- Search -->
    <div class="flex items-center gap-4 mb-6">
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Search news..."
        class="flex-1 px-4 py-2 border rounded-md shadow-sm focus:outline-none focus:ring focus:border-blue-400"
      />
      <button @click="fetchNews" class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700">
        Search
      </button>
    </div>

    <!-- Loading Spinner -->
    <div v-if="loading" class="text-center my-6 text-blue-600">
      <i class="fas fa-spinner fa-spin text-2xl"></i>
    </div>

    <!-- Articles -->
    <div v-if="articles.length" class="space-y-6">
      <div v-for="(article, index) in articles" :key="index" class="flex gap-4 border-b pb-4">
        <img
          v-if="article.urlToImage"
          :src="article.urlToImage"
          alt="news"
          class="w-32 h-24 object-cover rounded-md"
        />
        <div>
          <h2 class="text-lg font-semibold text-gray-800">{{ article.title }}</h2>
          <p class="text-sm text-gray-600">{{ article.source.name }} - {{ formatDate(article.publishedAt) }}</p>
          <a
            :href="article.url"
            target="_blank"
            class="inline-block mt-2 text-blue-500 hover:underline"
          >Read more</a>
        </div>
      </div>
    </div>

    <!-- Empty State -->
    <div v-if="!loading && articles.length === 0" class="text-center text-gray-500">
      No articles found.
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

// State
const articles = ref([])
const loading = ref(false)
const searchQuery = ref('apple')

// API Config
const API_KEY = '98b252020f1740c08a5b52b91c856c6a'
const BASE_URL = 'https://newsapi.org/v2/everything'

// Fetch Function
const fetchNews = async () => {
  loading.value = true
  try {
    const fromDate = '2025-04-08'
    const toDate = '2025-04-08'
    const response = await axios.get(BASE_URL, {
      params: {
        q: searchQuery.value,
        from: fromDate,
        to: toDate,
        sortBy: 'popularity',
        apiKey: API_KEY,
      },
    })
    articles.value = response.data.articles
  } catch (error) {
    console.error('Error fetching news:', error)
  } finally {
    loading.value = false
  }
}

// Initial fetch
fetchNews()

// Utility
const formatDate = (dateStr) => {
  return new Date(dateStr).toLocaleDateString()
}
</script>
