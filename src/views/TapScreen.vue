<template>
  <div class="screen tap-screen">
    <h2>{{ tapScreen.title }}</h2>
    
    <div class="cards-grid">
      <div 
        v-for="(card, index) in tapScreen.cards" 
        :key="index"
        class="card-wrapper"
        :style="{ 
          '--delay': `${index * 0.1}s`,
          '--color': getCardColor(card.color)
        }"
      >
        <div 
          class="card"
          :class="[card.color, { revealed: card.revealed }]"
          @click="$emit('reveal-card', index)"
        >
          <div class="card-front">
            <div class="card-icon" v-html="card.icon"></div>
            <div class="card-label">{{ card.label }}</div>
          </div>
          <div class="card-back">
            <div class="card-title">{{ card.label }}</div>
            <div class="card-description">{{ card.description }}</div>
          </div>
        </div>
      </div>
    </div>
    
    <NextButton @click="$emit('next-screen')" />
  </div>
</template>

<script setup>
import NextButton from '../components/NextButton.vue';

defineProps({
  tapScreen: {
    type: Object,
    required: true
  }
});

defineEmits(['reveal-card', 'next-screen']);

const getCardColor = (color) => {
  const colors = {
    teal: '#06b6d4',
    purple: '#a855f7',
    orange: '#f97316',
    blue: '#3b82f6'
  };
  
  return colors[color] || colors.blue;
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

.cards-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1.25rem;
  margin-bottom: 2.5rem;
  perspective: 1000px;
}

.card-wrapper {
  animation: cardAppear 0.6s ease forwards;
  animation-delay: var(--delay);
  opacity: 0;
  transform: translateY(20px);
}

.card {
  position: relative;
  height: 180px;
  cursor: pointer;
  transform-style: preserve-3d;
  transition: transform 0.8s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
  border-radius: 16px;
}

.card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, var(--color), color-mix(in srgb, var(--color), white 30%));
  opacity: 0;
  transition: opacity 0.3s ease;
  z-index: 0;
  border-radius: 16px;
}

.card:hover::before {
  opacity: 0.1;
}

.card-front, 
.card-back {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  -webkit-backface-visibility: hidden;
  backface-visibility: hidden;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 1.5rem;
  text-align: center;
  border-radius: 16px;
}

.card-front {
  background-color: var(--color, #3b82f6);
  color: white;
  z-index: 2;
  transform: rotateY(0deg);
}

.card-back {
  background-color: white;
  color: #333;
  transform: rotateY(180deg);
  overflow-y: auto;
  z-index: 1;
}

.card.revealed {
  transform: rotateY(180deg);
}

.card.teal .card-front {
  background-color: #06b6d4;
  --color: #06b6d4;
}

.card.purple .card-front {
  background-color: #a855f7;
  --color: #a855f7;
}

.card.orange .card-front {
  background-color: #f97316;
  --color: #f97316;
}

.card.blue .card-front {
  background-color: #3b82f6;
  --color: #3b82f6;
}

.card-icon {
  width: 40px;
  height: 40px;
  margin-bottom: 1rem;
  position: relative;
  z-index: 1;
}

.card-icon svg {
  width: 100%;
  height: 100%;
  stroke: currentColor;
  stroke-width: 2;
}

.card-label {
  font-weight: 600;
  font-size: 1.1rem;
  position: relative;
  z-index: 1;
}

.card-title {
  font-weight: 700;
  font-size: 1.2rem;
  margin-bottom: 0.75rem;
  color: var(--color);
}

.card-description {
  font-size: 0.95rem;
  line-height: 1.5;
  color: #4b5563;
}

@keyframes cardAppear {
  0% {
    opacity: 0;
    transform: translateY(20px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (min-width: 768px) {
  .cards-grid {
    gap: 1.5rem;
  }
  
  .card {
    height: 200px;
  }
  
  .card-icon {
    width: 48px;
    height: 48px;
  }
  
  .card-label {
    font-size: 1.2rem;
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
  
  .cards-grid {
    gap: 1rem;
  }
  
  .card {
    height: 160px;
  }
  
  .card-icon {
    width: 32px;
    height: 32px;
    margin-bottom: 0.75rem;
  }
  
  .card-label {
    font-size: 1rem;
  }
  
  .card-title {
    font-size: 1.1rem;
  }
  
  .card-description {
    font-size: 0.85rem;
  }
}
</style>