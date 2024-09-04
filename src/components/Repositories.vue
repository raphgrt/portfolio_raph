<template>
  <div :class="['repositories', { dark: isDarkMode }]">
    <h1 class="main-title">Repositories I Contribute To</h1>
    <div v-if="repositories.length" class="repo-grid">
      <div v-for="repo in displayedRepositories" :key="repo.id" class="repo-item">
        <img :src="repo.owner.avatar_url" alt="Owner Avatar" class="repo-avatar" />
        <div class="repo-details">
          <a :href="repo.html_url" target="_blank" class="repo-name">{{ repo.name }}</a>
          <p class="repo-description" v-html="repo.shortDescription"></p>
          <div class="repo-languages">
            <canvas :id="'chart-' + repo.id"></canvas>
          </div>
          <button class="more-info-btn" @click="repo.showMoreInfo = !repo.showMoreInfo">
            {{ repo.showMoreInfo ? 'Show less' : 'More info' }}
          </button>
          <div v-if="repo.showMoreInfo" class="more-info">
            <div v-if="repo.contributors.length > 1" class="repo-contributors">
              <h3>Contributors:</h3>
              <ul>
                <li v-for="contributor in repo.contributors" :key="contributor.id">
                  <img :src="contributor.avatar_url" alt="Contributor Avatar" class="contributor-avatar" />
                  <a :href="contributor.html_url" target="_blank">{{ contributor.login }}</a>
                </li>
              </ul>
            </div>
          </div>
          <p class="repo-last-push">Last push: {{ new Date(repo.pushed_at).toLocaleDateString() }}</p>
        </div>
      </div>
    </div>
    <div v-else class="error-message">
      {{ errorMessage }}
    </div>
    <button v-if="repositories.length > displayedRepositories.length" class="load-more-btn" @click="loadMoreRepositories">
      Load More
    </button>
  </div>
</template>

<script>
import axios from 'axios';
import { ref, onMounted, nextTick } from 'vue';
import { marked } from 'marked';
import Chart from 'chart.js/auto';

export default {
  name: 'Repositories',
  setup() {
    const repositories = ref([]);
    const displayedRepositories = ref([]);
    const isDarkMode = ref(false);
    const errorMessage = ref('');
    const itemsPerPage = 4;
    let currentPage = 1;

    const fetchRepositories = async (retryCount = 0) => {
      try {
        const response = await axios.get('https://api.github.com/user/repos', {
          headers: { Authorization: `token ${import.meta.env.VITE_GITHUB_TOKEN}` }
        });
        const repos = response.data.filter(repo => repo.permissions.push && !repo.private);

        repositories.value = await Promise.all(repos.map(async (repo) => {
          try {
            const contributorsResponse = await axios.get(repo.contributors_url, {
              headers: { Authorization: `token ${import.meta.env.VITE_GITHUB_TOKEN}` }
            });
            repo.contributors = contributorsResponse.data;
            const languagesResponse = await axios.get(repo.languages_url, {
              headers: { Authorization: `token ${import.meta.env.VITE_GITHUB_TOKEN}` }
            });
            repo.languages = languagesResponse.data;
          } catch (error) {
            console.error('Error fetching repo details:', error);
          }
          repo.showMoreInfo = false;
          return repo;
        }));

        repositories.value.sort((a, b) => new Date(b.pushed_at) - new Date(a.pushed_at));
        loadMoreRepositories();
      } catch (error) {
        if (error.response && error.response.status === 401) {
          errorMessage.value = 'Unauthorized: Please check your GitHub token.';
        } else if (error.response && error.response.status === 403 && retryCount < 3) {
          console.warn('Rate limit exceeded, retrying...');
          setTimeout(() => fetchRepositories(retryCount + 1), Math.pow(2, retryCount) * 1000);
        } else {
          errorMessage.value = 'Error fetching repositories. Please try again later.';
          console.error('Error fetching repositories:', error);
        }
      }
    };

    const loadMoreRepositories = () => {
      const start = (currentPage - 1) * itemsPerPage;
      const end = currentPage * itemsPerPage;
      displayedRepositories.value = repositories.value.slice(0, end);
      currentPage++;
      nextTick(() => {
        displayedRepositories.value.forEach(repo => {
          const ctx = document.getElementById(`chart-${repo.id}`).getContext('2d');
          new Chart(ctx, {
            type: 'pie',
            data: {
              labels: Object.keys(repo.languages),
              datasets: [{
                data: Object.values(repo.languages),
                backgroundColor: [
                  '#FF6384',
                  '#36A2EB',
                  '#FFCE56',
                  '#4BC0C0',
                  '#9966FF',
                  '#FF9F40'
                ]
              }]
            },
            options: {
              responsive: true,
              maintainAspectRatio: false
            }
          });
        });
      });
    };

    onMounted(() => {
      fetchRepositories();
    });

    return {
      repositories,
      displayedRepositories,
      isDarkMode,
      errorMessage,
      loadMoreRepositories,
    };
  }
};
</script>

<style scoped>
:root {
  --background-color-light: #f0f0f0;
  --background-color-dark: #1e1e1e;
  --text-color-light: #333333;
  --text-color-dark: #f0f0f0;
  --button-background-light: #007bff;
  --button-background-dark: #0056b3;
  --border-color-light: #dddddd;
  --border-color-dark: #444444;
  --hover-background-light: #e0e0e0;
  --hover-background-dark: #333333;
}

.repositories {
  padding: 20px;
  text-align: center;
  background-color: var(--background-color-light);
  color: var(--text-color-light);
  transition: background-color 0.3s, color 0.3s;
}

.repositories.dark {
  background-color: var(--background-color-dark);
  color: var(--text-color-dark);
}

.main-title {
  font-size: 3rem;
  margin-bottom: 20px;
}

.repo-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
  transition: all 0.3s ease;
}

.repo-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  border: 1px solid var(--border-color-light);
  border-radius: 8px;
  position: relative;
  transition: background-color 0.3s ease, border-color 0.3s ease;
}

.repo-item:hover {
  background-color: var(--hover-background-light);
}

.repositories.dark .repo-item {
  border-color: var(--border-color-dark);
}

.repositories.dark .repo-item:hover {
  background-color: var(--hover-background-dark);
}

.repo-avatar {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  margin-bottom: 15px;
}

.repo-details {
  text-align: left;
  width: 100%;
}

.repo-name {
  font-size: 1.5rem;
  font-weight: bold;
  color: var(--text-color-light);
  text-decoration: none;
}

.repo-name:hover {
  text-decoration: underline;
}

.repo-description {
  margin: 5px 0;
  color: #666;
}

.more-info-btn {
  margin-top: 10px;
  padding: 10px 20px;
  border: none;
  background-color: var(--button-background-light);
  color: white;
  border-radius: 25px;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.3s ease;
}

.more-info-btn:hover {
  background-color: var(--button-background-dark);
  transform: scale(1.05);
}

.more-info {
  margin-top: 10px;
}

.repo-languages {
  position: absolute;
  top: 10px;
  right: 10px;
  width: 100px;
  height: 100px;
}

.repo-contributors {
  margin-top: 10px;
}

.repo-contributors ul {
  list-style: none;
  padding: 0;
}

.repo-contributors li {
  display: flex;
  align-items: center;
  margin-bottom: 5px;
}

.contributor-avatar {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  margin-right: 8px;
}

.repo-last-push {
  margin-top: 10px;
  color: #666;
}

.load-more-btn {
  margin-top: 20px;
  padding: 10px 20px;
  border: none;
  background-color: var(--button-background-light);
  color: white;
  border-radius: 25px;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.3s ease;
}

.load-more-btn:hover {
  background-color: var(--button-background-dark);
  transform: scale(1.05);
}

.error-message {
  color: red;
  font-size: 1.2rem;
  margin-top: 20px;
}
</style>