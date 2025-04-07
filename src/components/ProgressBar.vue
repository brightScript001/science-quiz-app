<template>
  <div class="progress-container">
    <div class="progress-bar">
      <div
        class="progress-fill"
        :style="{ width: `${(current / total) * 100}%` }"
      ></div>
    </div>
    <div class="progress-steps">
      <div
        v-for="step in total"
        :key="step"
        class="progress-step"
        :class="{ active: step <= current, completed: step < current }"
      >
        <div class="step-indicator">
          <span class="step-number">{{ step }}</span>
          <svg v-if="step < current" class="check-icon" viewBox="0 0 24 24">
            <path
              d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41L9 16.17z"
            />
          </svg>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  current: {
    type: Number,
    required: true,
  },
  total: {
    type: Number,
    required: true,
  },
});
</script>

<style scoped>
.progress-container {
  position: relative;
  margin-bottom: 0.5rem;
}

.progress-bar {
  height: 4px;
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 2px;
  overflow: hidden;
  margin-bottom: 0.75rem;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #3b82f6, #8b5cf6);
  border-radius: 2px;
  transition: width 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

.progress-steps {
  display: flex;
  justify-content: space-between;
  position: relative;
}

.progress-step {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.step-indicator {
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.1);
  display: flex;
  align-items: center;
  justify-content: center;
  color: rgba(255, 255, 255, 0.6);
  font-size: 0.75rem;
  font-weight: 600;
  transition: all 0.3s ease;
}

.progress-step.active .step-indicator {
  background-color: #3b82f6;
  color: white;
  box-shadow: 0 0 0 4px rgba(59, 130, 246, 0.3);
}

.progress-step.completed .step-indicator {
  background-color: #8b5cf6;
  color: white;
}

.check-icon {
  width: 14px;
  height: 14px;
  fill: white;
}

.step-number {
  display: flex;
  align-items: center;
  justify-content: center;
}

@media (max-width: 480px) {
  .step-indicator {
    width: 20px;
    height: 20px;
    font-size: 0.7rem;
  }

  .check-icon {
    width: 12px;
    height: 12px;
  }
}
</style>
