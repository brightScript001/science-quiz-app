<template>
  <div class="screen swipe-screen">
    <h2>{{ swipeScreen.title }}</h2>

    <div class="swipe-options">
      <div class="swipe-option left">
        <span class="arrow">←</span> {{ swipeScreen.leftOption }}
      </div>
      <div class="swipe-option right">
        {{ swipeScreen.rightOption }} <span class="arrow">→</span>
      </div>
    </div>

    <CardStack
      :currentCard="swipeScreen.currentCard"
      @handle-swipe="handleSwipeCard"
    />

    <div class="swipe-buttons">
      <button class="btn-swipe btn-left" @click="handleSwipeCard('left')">
        <svg
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <polyline points="15 18 9 12 15 6"></polyline>
        </svg>
      </button>
      <button class="btn-swipe btn-right" @click="handleSwipeCard('right')">
        <svg
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <polyline points="9 18 15 12 9 6"></polyline>
        </svg>
      </button>
    </div>

    <NextButton @click="$emit('next-screen')" />
  </div>
</template>

<script setup>
import { ref } from "vue";
import NextButton from "../components/NextButton.vue";
import CardStack from "../components/CardStack.vue";

const props = defineProps({
  swipeScreen: {
    type: Object,
    required: true,
  },
});

const emit = defineEmits(["handle-swipe", "next-screen"]);

const handleSwipeCard = (direction) => {
  emit("handle-swipe", direction);
};
</script>

<style scoped>
.screen {
  padding: 1.5rem;
  min-height: 70vh;
  display: flex;
  flex-direction: column;
}

h2 {
  font-size: 1.25rem;
  font-weight: 600;
  text-align: center;
  margin-bottom: 2rem;
  color: white;
  line-height: 1.4;
  animation: fadeIn 0.8s ease forwards;
}

.swipe-options {
  display: flex;
  justify-content: space-between;
  margin-bottom: 1rem;
  font-size: 1rem;
  color: rgba(255, 255, 255, 0.7);
  padding: 0 0.5rem;
  animation: fadeIn 0.8s ease 0.2s forwards;
  opacity: 0;
}

.swipe-option {
  display: flex;
  align-items: center;
  font-weight: 500;
}

.swipe-option.left {
  margin-right: auto;
}

.swipe-option.right {
  margin-left: auto;
}

.arrow {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  margin: 0 0.5rem;
}

.swipe-buttons {
  display: flex;
  justify-content: space-between;
  margin-bottom: 2.5rem;
  padding: 0 2rem;
  animation: fadeIn 0.8s ease 0.4s forwards;
  opacity: 0;
}

.btn-swipe {
  width: 56px;
  height: 56px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  color: white;
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.btn-swipe svg {
  width: 24px;
  height: 24px;
  stroke: currentColor;
}

.btn-swipe.btn-left {
  background: linear-gradient(135deg, #f43f5e, #ec4899);
}

.btn-swipe.btn-right {
  background: linear-gradient(135deg, #10b981, #22c55e);
}

.btn-swipe:hover {
  transform: translateY(-3px) scale(1.05);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.15);
}

.btn-swipe:active {
  transform: translateY(0) scale(0.98);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (min-width: 768px) {
  .btn-swipe {
    width: 64px;
    height: 64px;
  }

  .btn-swipe svg {
    width: 28px;
    height: 28px;
  }
}

@media (max-width: 480px) {
  .screen {
    padding: 1.25rem;
  }

  h2 {
    font-size: 1.1rem;
    margin-bottom: 1.5rem;
  }

  .swipe-options {
    font-size: 0.9rem;
  }

  .swipe-buttons {
    padding: 0 1.5rem;
    margin-bottom: 2rem;
  }

  .btn-swipe {
    width: 48px;
    height: 48px;
  }

  .btn-swipe svg {
    width: 20px;
    height: 20px;
  }
}
</style>
