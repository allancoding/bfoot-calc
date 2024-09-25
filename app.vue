<template>
  <nav class="navbar">
    <div class="logo-name">
      <img src="/logo/logo.png" alt="Logo" class="logo">
      <span class="name">Board Foot Calculator</span>
    </div>
    <a href="https://github.com/your-repo" class="github-link">
      <img src="https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png" alt="GitHub" class="github-icon">
    </a>
  </nav>
</template>

<style>
body, html {
  margin: 0;
  padding: 0;
  background-color: #000;
}

.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  background-color: #333;
  color: white;
}

.github-link {
  display: flex;
  align-items: center;
}

.github-icon {
  width: 24px;
  height: 24px;
}

.logo-name {
  display: flex;
  align-items: center;
}

.logo {
  width: 40px;
  height: 40px;
  margin-right: 10px;
}

.name {
  font-size: 1.5em;
}
</style>