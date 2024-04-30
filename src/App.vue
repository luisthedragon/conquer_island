<template>
  <div id="app">
    <div class="background">
      <div id="nav">
        <router-link to="/">Play!</router-link> |
        <router-link to="/how-to-play">How to play</router-link>
      </div>
      <router-view />
    </div>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #ffffff;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #ffffff;
}

#nav a.router-link-exact-active {
  color: #42b983;
}

.background {
  width: 100%; /* Full width */
  height: 100%;
  position: fixed; /* Stay in place */
  z-index: 1; /* Sit on top */
  left: 0;
  top: 0;
  /* background-color: rgba(245, 22, 22, 0.959); */
  background-image: url("@/../assets/sprites/og/forest_og.png");
  background-size: cover;
  animation-name: fadeIn;
  animation-duration: 2.5s;
  animation-iteration-count: 1;
  animation-fill-mode: forwards;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style>
