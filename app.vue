<template>
  <nav class="navbar">
    <div class="logo-name">
      <img src="/logo/logo.png" alt="Logo" class="logo">
      <span class="name">Board Foot Calculator</span>
    </div>
    <a href="https://github.com/allancoding/bfoot-calc" class="github-link">
      <Icon name="uil:github" style="color: white" class="github-icon" />
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
  background-color: #000;
  color: #fff;
  box-shadow: 0 0 30px rgba(255, 255, 255, 0.5);
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