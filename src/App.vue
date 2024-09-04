<script setup>
import { ref, watch } from 'vue';
import Contacts from "@/components/Contacts.vue";
import Home from "@/components/Home.vue";
import Projects from "@/components/Projects.vue";
import Resume from "@/components/Resume.vue";
import Repositories from "@/components/Repositories.vue";

const userTheme = ref('light-theme');

const toggleTheme = () => {
  userTheme.value = userTheme.value === 'light-theme' ? 'dark-theme' : 'light-theme';
};

watch(userTheme, (newTheme) => {
  document.documentElement.className = newTheme;
});
</script>

<template>
  <header class="VPNav nav-bar fixed">
    <nav>
      <ul>
        <li><a href="#home">Home</a></li>
        <li><a href="#resume">Resume</a></li>
        <li><a href="#projects">Projects</a></li>
        <li><a href="#contacts">Contacts</a></li>
      </ul>
    </nav>
    <div class="theme-switch">
      <div class="card">
        <input
            @change="toggleTheme"
            id="checkbox"
            type="checkbox"
            class="switch-checkbox"
        />
        <label for="checkbox" class="switch-label">
          <span class="sun-icon">☀️</span>
          <span class="moon-icon">🌙</span>
          <div
              class="switch-toggle"
              :class="{ 'switch-toggle-checked': userTheme === 'dark-theme' }"
          ></div>
        </label>
      </div>
    </div>
  </header>
  <main>
    <section id="home">
      <Home />
    </section>
    <section id="resume">
      <Resume />
    </section>
    <section id="projects">
      <Projects />
      <Repositories />
    </section>
    <section id="contacts">
      <Contacts />
    </section>
  </main>
  <footer>
    <p>Make with <3 and Vue</p>
    <p>© 2024 Raphaël</p>
  </footer>
</template>

<style>
.light-theme {
  --background-color: linear-gradient(135deg, #f6d365 0%, #fda085 100%);
  --text-color: rgb(34, 54, 71);
}

.dark-theme {
  --background-color: linear-gradient(135deg, #2b5876 0%, #4e4376 100%);
  --text-color: white;
}

body {
  background: var(--background-color);
  color: var(--text-color);
  margin: 0;
  font-family: Arial, sans-serif;
}

footer {
  display: flex;
  justify-content: space-between;
  padding: 1rem;
  background-color: #333;
  color: white;
}

.card {
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  margin: 16px;
  transition: background-color 0.3s ease;
}

.switch-checkbox {
  display: none;
}

.switch-label {
  display: flex;
  align-items: center;
  cursor: pointer;
  width: 50px;
  height: 25px;
  background-color: #ccc;
  border-radius: 25px;
  position: relative;
  transition: background-color 0.3s ease;
}

.switch-label .sun-icon,
.switch-label .moon-icon {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  font-size: 16px;
}

.switch-label .sun-icon {
  left: 5px;
}

.switch-label .moon-icon {
  right: 5px;
}

.switch-toggle {
  position: absolute;
  top: 2px;
  left: 2px;
  width: 21px;
  height: 21px;
  background-color: white;
  border-radius: 50%;
  transition: transform 0.3s ease;
}

.switch-toggle-checked {
  transform: translateX(25px);
}

nav ul {
  list-style: none;
  display: flex;
  gap: 1rem;
}

nav ul li a {
  text-decoration: none;
  color: var(--text-color);
  font-weight: bold;
}

nav ul li a:hover {
  text-decoration: underline;
}

.VPNav.fixed {
  position: fixed;
  display: flex;
  justify-content: space-between;
  align-items: center;
  top: 0;
  width: 100%;
  z-index: 1000;
}

nav {
  display: flex;
  gap: 1rem;
}

.theme-switch {
  margin-right: 16px;
}

main {
  padding-top: 60px; /* Adjust this value based on the height of your navbar */
}

html {
  scroll-behavior: smooth;
  background: var(--background-color);
}
</style>