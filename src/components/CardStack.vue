<template>
  <div class="card-stack-container" :class="{ 'is-swiping': isSwiping }">
    <!-- Stacked background cards -->
    <div class="stacked-card card-back-3"></div>
    <div class="stacked-card card-back-2"></div>
    <div class="stacked-card card-back-1"></div>

    <!-- Direction indicators -->
    <div class="direction-indicators">
      <div
        class="direction-indicator left"
        :class="{ active: swipeDirection === 'left' }"
      >
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
        <span>Physical</span>
      </div>
      <div
        class="direction-indicator right"
        :class="{ active: swipeDirection === 'right' }"
      >
        <span>Chemical</span>
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
      </div>
    </div>

    <!-- Main swipeable card -->
    <div
      ref="swipeCard"
      class="swipe-card"
      :class="{
        'swipe-left': swipeDirection === 'left',
        'swipe-right': swipeDirection === 'right',
        'swipe-exit': isExiting,
        'card-color-0': cardColorIndex === 0,
        'card-color-1': cardColorIndex === 1,
        'card-color-2': cardColorIndex === 2,
        'card-color-3': cardColorIndex === 3,
      }"
      @touchstart="touchStart"
      @touchmove="touchMove"
      @touchend="touchEnd"
      @mousedown="mouseDown"
      @mousemove="mouseMove"
      @mouseup="mouseUp"
      @mouseleave="mouseUp"
      :style="cardStyle"
    >
      <div class="card-content">
        <div class="card-icon" v-html="currentCard.icon"></div>
        <div class="card-label">{{ currentCard.label }}</div>
      </div>

      <!-- Swipe effect overlay -->
      <div class="swipe-overlay left" :style="{ opacity: leftOverlayOpacity }">
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
      </div>
      <div
        class="swipe-overlay right"
        :style="{ opacity: rightOverlayOpacity }"
      >
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
      </div>
    </div>

    <!-- Feedback indicators -->
    <div
      class="feedback-indicator correct"
      :class="{ active: showCorrectFeedback }"
    >
      <svg
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        stroke-width="2"
        stroke-linecap="round"
        stroke-linejoin="round"
      >
        <path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"></path>
        <polyline points="22 4 12 14.01 9 11.01"></polyline>
      </svg>
      <span>Correct!</span>
    </div>
    <div
      class="feedback-indicator incorrect"
      :class="{ active: showIncorrectFeedback }"
    >
      <svg
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        stroke-width="2"
        stroke-linecap="round"
        stroke-linejoin="round"
      >
        <circle cx="12" cy="12" r="10"></circle>
        <line x1="15" y1="9" x2="9" y2="15"></line>
        <line x1="9" y1="9" x2="15" y2="15"></line>
      </svg>
      <span>Try Again</span>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onBeforeUnmount, watch } from "vue";

const props = defineProps({
  currentCard: {
    type: Object,
    required: true,
  },
});

const emit = defineEmits(["handle-swipe"]);

// Touch/Mouse handling for swipe
const touchStartX = ref(0);
const touchStartY = ref(0);
const swipeOffset = ref(0);
const swipeDirection = ref(null);
const isDragging = ref(false);
const isSwiping = ref(false);
const isExiting = ref(false);
const swipeCard = ref(null);
const swipeThreshold = 100;
const maxRotation = 15;
const maxSwipeDistance = 150;

// Card color rotation
const cardColorIndex = ref(0);
const cardColors = ["#4facfe", "#f97316", "#a855f7", "#10b981"];

// Feedback indicators
const showCorrectFeedback = ref(false);
const showIncorrectFeedback = ref(false);

// Watch for card changes to update color
watch(
  () => props.currentCard.label,
  () => {
    // Rotate to next color when card changes
    cardColorIndex.value = (cardColorIndex.value + 1) % cardColors.length;
  }
);

// Computed styles
const cardStyle = computed(() => {
  const rotate = (swipeOffset.value / maxSwipeDistance) * maxRotation;
  return {
    transform: `translateX(${swipeOffset.value}px) rotate(${rotate}deg)`,
  };
});

const leftOverlayOpacity = computed(() => {
  if (swipeOffset.value < 0) {
    return Math.min(Math.abs(swipeOffset.value) / swipeThreshold, 1);
  }
  return 0;
});

const rightOverlayOpacity = computed(() => {
  if (swipeOffset.value > 0) {
    return Math.min(swipeOffset.value / swipeThreshold, 1);
  }
  return 0;
});

// Touch handlers for swipe
const touchStart = (event) => {
  touchStartX.value = event.touches[0].clientX;
  touchStartY.value = event.touches[0].clientY;
  isDragging.value = true;
  isSwiping.value = true;
};

const touchMove = (event) => {
  if (!isDragging.value) return;

  const currentX = event.touches[0].clientX;
  const currentY = event.touches[0].clientY;
  const diffX = currentX - touchStartX.value;
  const diffY = currentY - touchStartY.value;

  // If vertical movement is greater than horizontal, don't swipe
  if (Math.abs(diffY) > Math.abs(diffX) * 1.5) {
    return;
  }

  // Limit the swipe distance with diminishing returns
  const sign = diffX < 0 ? -1 : 1;
  const absOffset = Math.min(Math.abs(diffX), maxSwipeDistance);
  swipeOffset.value = sign * absOffset;

  // Determine swipe direction
  if (diffX < -50) {
    swipeDirection.value = "left";
  } else if (diffX > 50) {
    swipeDirection.value = "right";
  } else {
    swipeDirection.value = null;
  }
};

const touchEnd = () => {
  if (!isDragging.value) return;

  isDragging.value = false;

  if (Math.abs(swipeOffset.value) > swipeThreshold) {
    // Complete the swipe
    completeSwipe();
  } else {
    // Reset if not swiped far enough
    resetSwipe();
  }
};

// Mouse handlers for swipe (desktop support)
const mouseDown = (event) => {
  touchStartX.value = event.clientX;
  touchStartY.value = event.clientY;
  isDragging.value = true;
  isSwiping.value = true;

  // Prevent text selection during swipe
  event.preventDefault();
};

const mouseMove = (event) => {
  if (!isDragging.value) return;

  const currentX = event.clientX;
  const currentY = event.clientY;
  const diffX = currentX - touchStartX.value;
  const diffY = currentY - touchStartY.value;

  // If vertical movement is greater than horizontal, don't swipe
  if (Math.abs(diffY) > Math.abs(diffX) * 1.5) {
    return;
  }

  // Limit the swipe distance with diminishing returns
  const sign = diffX < 0 ? -1 : 1;
  const absOffset = Math.min(Math.abs(diffX), maxSwipeDistance);
  swipeOffset.value = sign * absOffset;

  // Determine swipe direction
  if (diffX < -50) {
    swipeDirection.value = "left";
  } else if (diffX > 50) {
    swipeDirection.value = "right";
  } else {
    swipeDirection.value = null;
  }
};

const mouseUp = () => {
  if (!isDragging.value) return;

  isDragging.value = false;

  if (Math.abs(swipeOffset.value) > swipeThreshold) {
    // Complete the swipe
    completeSwipe();
  } else {
    // Reset if not swiped far enough
    resetSwipe();
  }
};

const completeSwipe = () => {
  const direction = swipeDirection.value;

  // Check if the swipe is correct
  const isCorrect = direction === props.currentCard.correctAnswer;

  // Show feedback
  if (isCorrect) {
    showCorrectFeedback.value = true;
    setTimeout(() => {
      showCorrectFeedback.value = false;
    }, 1500);
  } else {
    showIncorrectFeedback.value = true;
    setTimeout(() => {
      showIncorrectFeedback.value = false;
    }, 1500);
  }

  // Animate the card off screen with exit animation
  isExiting.value = true;
  const targetX = direction === "left" ? -window.innerWidth : window.innerWidth;
  const duration = 500;

  animateSwipe(targetX, duration).then(() => {
    // Emit the swipe event
    const result = emit("handle-swipe", direction);

    // If the swipe was successful (correct answer), prepare for next card
    if (isCorrect) {
      // Reset position but keep exiting class for a moment
      swipeOffset.value = 0;

      // After a short delay, reset everything for the next card
      setTimeout(() => {
        isExiting.value = false;
        swipeDirection.value = null;
        isSwiping.value = false;
      }, 300);
    } else {
      // If incorrect, just reset the card
      resetSwipe();
    }
  });
};

const resetSwipe = () => {
  // Animate back to center
  isExiting.value = false;
  animateSwipe(0, 200).then(() => {
    swipeDirection.value = null;
    isSwiping.value = false;
  });
};

const animateSwipe = (targetX, duration) => {
  return new Promise((resolve) => {
    const startX = swipeOffset.value;
    const distance = targetX - startX;
    const startTime = performance.now();

    const animate = (currentTime) => {
      const elapsedTime = currentTime - startTime;
      const progress = Math.min(elapsedTime / duration, 1);
      const easeProgress = easeOutCubic(progress);

      swipeOffset.value = startX + distance * easeProgress;

      if (progress < 1) {
        requestAnimationFrame(animate);
      } else {
        resolve();
      }
    };

    requestAnimationFrame(animate);
  });
};

// Easing function for smoother animation
const easeOutCubic = (x) => {
  return 1 - Math.pow(1 - x, 3);
};

// Clean up event listeners
onBeforeUnmount(() => {
  isDragging.value = false;
  isSwiping.value = false;
});
</script>

<style scoped>
.card-stack-container {
  position: relative;
  height: 300px;
  width: 100%;
  max-width: 320px;
  margin: 1.5rem auto 2.5rem;
  perspective: 1000px;
}

.stacked-card {
  position: absolute;
  width: 100%;
  height: 220px;
  border-radius: 20px;
  background: linear-gradient(135deg, #ff9d6c, #ff9a8b);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
}

.card-back-3 {
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 1;
  transform: translateY(16px) scale(0.85);
  opacity: 0.5;
  background: linear-gradient(135deg, #ffb347, #ffcc33);
}

.card-back-2 {
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 2;
  transform: translateY(8px) scale(0.9);
  opacity: 0.7;
  background: linear-gradient(135deg, #ff9a8b, #ff6a88);
}

.card-back-1 {
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 3;
  transform: translateY(4px) scale(0.95);
  opacity: 0.9;
  background: linear-gradient(135deg, #36d1dc, #5b86e5);
}

.is-swiping .stacked-card {
  transform: translateY(0) scale(1);
}

.swipe-card {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 4;
  color: white;
  border-radius: 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  transition: box-shadow 0.3s ease, opacity 0.3s ease, transform 0.3s ease;
  box-shadow: 0 15px 35px rgba(0, 0, 0, 0.2);
  cursor: grab;
  overflow: hidden;
  will-change: transform;
}

/* Different card colors */
.swipe-card.card-color-0 {
  background: linear-gradient(135deg, #4facfe, #00f2fe);
}

.swipe-card.card-color-1 {
  background: linear-gradient(135deg, #f97316, #fb923c);
}

.swipe-card.card-color-2 {
  background: linear-gradient(135deg, #a855f7, #c084fc);
}

.swipe-card.card-color-3 {
  background: linear-gradient(135deg, #10b981, #34d399);
}

.swipe-card:active {
  cursor: grabbing;
}

.swipe-card.swipe-exit {
  opacity: 0;
  transition: transform 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275),
    opacity 0.5s ease;
}

.card-content {
  position: relative;
  z-index: 2;
  padding: 2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
}

.card-icon {
  width: 60px;
  height: 60px;
  margin-bottom: 1.5rem;
}

.card-icon svg {
  width: 100%;
  height: 100%;
  stroke: white;
  stroke-width: 2;
}

.card-label {
  font-weight: 700;
  font-size: 1.5rem;
  letter-spacing: 0.5px;
}

/* Direction indicators */
.direction-indicators {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 0;
  display: flex;
  justify-content: space-between;
  pointer-events: none;
}

.direction-indicator {
  display: flex;
  align-items: center;
  color: white;
  opacity: 0.5;
  font-weight: 600;
  font-size: 0.9rem;
  transition: all 0.3s ease;
  padding: 0 1.5rem;
  transform: translateY(50px);
}

.direction-indicator.active {
  opacity: 1;
  transform: translateY(0);
}

.direction-indicator svg {
  width: 24px;
  height: 24px;
}

.direction-indicator.left {
  justify-content: flex-start;
}

.direction-indicator.right {
  justify-content: flex-end;
}

.direction-indicator.left svg {
  margin-right: 0.5rem;
}

.direction-indicator.right svg {
  margin-left: 0.5rem;
}

/* Swipe overlays */
.swipe-overlay {
  position: absolute;
  top: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0;
  transition: opacity 0.2s ease;
  z-index: 1;
}

.swipe-overlay svg {
  width: 60px;
  height: 60px;
  stroke: white;
  stroke-width: 3;
  filter: drop-shadow(0 0 8px rgba(0, 0, 0, 0.3));
}

.swipe-overlay.left {
  background: linear-gradient(90deg, rgba(244, 63, 94, 0.8), transparent);
  left: 0;
  justify-content: flex-start;
  padding-left: 2rem;
}

.swipe-overlay.right {
  background: linear-gradient(90deg, transparent, rgba(34, 197, 94, 0.8));
  right: 0;
  justify-content: flex-end;
  padding-right: 2rem;
}

/* Feedback indicators */
.feedback-indicator {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%) scale(0.5);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: rgba(0, 0, 0, 0.7);
  backdrop-filter: blur(8px);
  -webkit-backdrop-filter: blur(8px);
  border-radius: 16px;
  padding: 1.5rem;
  opacity: 0;
  z-index: 10;
  transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  pointer-events: none;
}

.feedback-indicator.active {
  opacity: 1;
  transform: translate(-50%, -50%) scale(1);
}

.feedback-indicator svg {
  width: 48px;
  height: 48px;
  margin-bottom: 0.75rem;
}

.feedback-indicator span {
  font-weight: 600;
  font-size: 1.2rem;
}

.feedback-indicator.correct {
  color: #10b981;
}

.feedback-indicator.incorrect {
  color: #f43f5e;
}

.feedback-indicator.correct svg {
  stroke: #10b981;
}

.feedback-indicator.incorrect svg {
  stroke: #f43f5e;
}

@media (min-width: 768px) {
  .card-stack-container {
    height: 320px;
    max-width: 360px;
  }

  .stacked-card {
    height: 240px;
  }

  .card-icon {
    width: 70px;
    height: 70px;
  }

  .card-label {
    font-size: 1.75rem;
  }
}

@media (max-width: 480px) {
  .card-stack-container {
    height: 260px;
    max-width: 280px;
    margin: 1rem auto 2rem;
  }

  .stacked-card {
    height: 200px;
  }

  .card-icon {
    width: 50px;
    height: 50px;
    margin-bottom: 1rem;
  }

  .card-label {
    font-size: 1.3rem;
  }

  .direction-indicator {
    font-size: 0.8rem;
    padding: 0 1rem;
  }

  .direction-indicator svg {
    width: 20px;
    height: 20px;
  }

  .swipe-overlay svg {
    width: 50px;
    height: 50px;
  }

  .feedback-indicator svg {
    width: 40px;
    height: 40px;
  }

  .feedback-indicator span {
    font-size: 1.1rem;
  }
}
</style>
