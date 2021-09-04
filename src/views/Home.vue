<template>
  <div class="container">
    <h1>MEMORY MODIFICATO</h1>
    <img src="https://cdn.frankerfacez.com/avatar/twitch/148287276" alt="based" />
    <div class="btn-container">
      <router-link class="mode-selector easy" to="/game/facile">Facile</router-link>
      <router-link class="mode-selector medium" to="/game/medio">Medio</router-link>
      <router-link class="mode-selector difficult" to="/game/difficile">Difficile</router-link>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Home',
};
</script>

<style lang="scss" scoped>
img {
  height: 10vh;
}

h1 {
  font-size: 4rem;
}

.container {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.btn-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.mode-selector {
  width: 10vw;
  min-width: fit-content;
  padding: 0.7rem 1.3rem;
  border-radius: 0.3rem;
  margin: 0.5rem 0;
  color: #fff;
  text-decoration: none;
  font-size: 1.3rem;
  font-weight: 700;
}

.easy {
  background: #2a9d90;
  &:hover {
    background: #228176;
  }
}

.medium {
  background: #f4a261;
  &:hover {
    background: #ef7a1a;
  }
}

.difficult {
  background: #e76f51;
  &:hover {
    background: #e24d28;
  }
}
</style>
