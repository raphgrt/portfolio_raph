<template>
  <div class="contacts">
    <h1>My Contact</h1>
    <ul>
      <li>
        <a href="www.linkedin.com/in/raphaël-georget-87328b225" target="_blank">
          <img src="@/assets/linkedin.svg" alt="LinkedIn" class="icon" /> LinkedIn
        </a>
      </li>
      <li>
        <a href="https://discordapp.com/users/LaMouize" target="_blank">
          <img src="@/assets/discord.svg" alt="Discord" class="icon" /> Discord
        </a>
      </li>
      <li>
        <a href="https://github.com/raph-grt" target="_blank">
          <img src="@/assets/github.svg" alt="GitHub" class="icon" /> GitHub
        </a>
      </li>
      <li>
        <a href="mailto:raphael.georgetpro@orange.fr">
          <img src="@/assets/mail.svg" alt="Mail" class="icon" /> Mail
        </a>
      </li>
      <li>
        <a href="tel:+33000000">
          <img src="@/assets/phone.svg" alt="Phone" class="icon" /> Phone
        </a>
      </li>
    </ul>
  </div>
</template>

<style>
.contacts {
  padding: 20px;
  text-align: center;
}

.contacts h1 {
  margin-bottom: 20px;
  font-size: 2rem;
}

.contacts ul {
  list-style: none;
  padding: 0;
}

.contacts ul li {
  margin-bottom: 10px;
}

.contacts ul li a {
  text-decoration: none;
  color: var(--text-color);
  font-weight: bold;
  display: flex;
  align-items: center;
  justify-content: center;
}

.contacts ul li a:hover {
  text-decoration: underline;
}

.contacts ul li a .icon {
  width: 24px;
  height: 24px;
  margin-right: 8px;
}

@media (min-width: 1024px) {
  .contacts {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
}
</style>

<script setup lang="ts">
</script>