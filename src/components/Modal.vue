<template>
  <transition name="fade">
    <div v-if="visible" class="modal">
      <h1>POGGERS!</h1>

      <router-link class="back-to-home-btn" to="/">Torna alla home</router-link>
    </div>
  </transition>
</template>

<script>
export default {
  props: {
    visible: {
      type: Boolean,
      required: true,
    },
  },
};
</script>

<style lang="scss" scoped>
.modal {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: #242424bf;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.back-to-home-btn {
  background-color: #228176;
  color: #fff;
  font-size: 2rem;
  width: fit-content;
  padding: 0.5rem 1rem;
  border-radius: 0.5rem;
  text-decoration: none;
  &:hover {
    background-color: #215759;
  }
  margin-bottom: 35vh;
}

h1 {
  color: #e2b33c;
  font-size: 3rem;
}

.fade-enter-active {
  animation: fade-in 2s;
}
.fade-leave-active {
  animation: fade-in 2s reverse;
}

@keyframes fade-in {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
</style>
