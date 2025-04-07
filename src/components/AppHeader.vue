<template>
  <header>
    <div class="header-content">
      <h1>Lesson {{ currentLesson }}</h1>
      <ProgressBar :current="currentScreen" :total="totalScreens" />
    </div>
  </header>
</template>

<script setup>
import ProgressBar from "./ProgressBar.vue";

defineProps({
  currentLesson: {
    type: String,
    required: true,
  },
  currentScreen: {
    type: Number,
    required: true,
  },
  totalScreens: {
    type: Number,
    required: true,
  },
});
</script>

<style scoped>
header {
  padding: 1.5rem;
  position: relative;
  z-index: 10;
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  background-color: rgba(255, 255, 255, 0.1);
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.header-content {
  max-width: 1200px;
  margin: 0 auto;
}

h1 {
  font-size: 1.1rem;
  font-weight: 600;
  color: white;
  margin-bottom: 0.75rem;
  letter-spacing: 0.5px;
}

@media (max-width: 480px) {
  header {
    padding: 1rem;
  }

  h1 {
    font-size: 1rem;
  }
}
</style>
