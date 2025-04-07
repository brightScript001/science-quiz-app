<template>
  <div class="screen result-screen">
    <div v-if="isSuccess" class="result success">
      <div class="success-icon">
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
      </div>
      <h2>Great Job</h2>
      <p>You've completed this section successfully!</p>
    </div>
    <div v-else class="result error">
      <div class="error-icon">
        <svg
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <circle cx="12" cy="12" r="10"></circle>
          <line x1="12" y1="8" x2="12" y2="12"></line>
          <line x1="12" y1="16" x2="12.01" y2="16"></line>
        </svg>
      </div>
      <h2>Try Again</h2>
      <p>{{ resultMessage }}</p>
    </div>

    <div class="button-group">
      <NextButton
        v-if="!isSuccess"
        @click="$emit('try-again')"
        :primary="false"
        icon="refresh"
      >
        Try Again
      </NextButton>

      <NextButton @click="$emit('next-screen')"> Continue </NextButton>
    </div>
  </div>
</template>

<script setup>
import NextButton from "../components/NextButton.vue";

defineProps({
  isSuccess: {
    type: Boolean,
    required: true,
  },
  resultMessage: {
    type: String,
    default: "",
  },
});

defineEmits(["try-again", "next-screen"]);
</script>

<style scoped>
.screen {
  padding: 1.5rem;
  min-height: 70vh;
  display: flex;
  flex-direction: column;
}

.result {
  text-align: center;
  padding: 3rem 1rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  flex: 1;
}

.success-icon,
.error-icon {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 1.5rem;
  animation: scaleIn 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
}

.success-icon {
  background: linear-gradient(135deg, #10b981, #22c55e);
  box-shadow: 0 10px 25px rgba(16, 185, 129, 0.3);
}

.error-icon {
  background: linear-gradient(135deg, #f43f5e, #ec4899);
  box-shadow: 0 10px 25px rgba(244, 63, 94, 0.3);
}

.success-icon svg,
.error-icon svg {
  width: 40px;
  height: 40px;
  stroke: white;
  stroke-width: 3;
}

.result.success h2 {
  font-size: 2.5rem;
  color: #10b981;
  margin-bottom: 1rem;
  animation: fadeInUp 0.5s ease 0.2s forwards;
  opacity: 0;
  transform: translateY(20px);
}

.result.error h2 {
  font-size: 2rem;
  color: #f43f5e;
  margin-bottom: 1rem;
  animation: fadeInUp 0.5s ease 0.2s forwards;
  opacity: 0;
  transform: translateY(20px);
}

.result p {
  color: rgba(255, 255, 255, 0.7);
  margin-bottom: 2rem;
  animation: fadeInUp 0.5s ease 0.4s forwards;
  opacity: 0;
  transform: translateY(20px);
  max-width: 300px;
  line-height: 1.6;
}

.button-group {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  width: 100%;
  animation: fadeInUp 0.5s ease 0.6s forwards;
  opacity: 0;
  transform: translateY(20px);
}

@keyframes scaleIn {
  0% {
    transform: scale(0);
    opacity: 0;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (min-width: 768px) {
  .success-icon,
  .error-icon {
    width: 100px;
    height: 100px;
  }

  .success-icon svg,
  .error-icon svg {
    width: 50px;
    height: 50px;
  }

  .result.success h2 {
    font-size: 3rem;
  }

  .result.error h2 {
    font-size: 2.5rem;
  }

  .result p {
    font-size: 1.1rem;
    max-width: 350px;
  }
}

@media (max-width: 480px) {
  .screen {
    padding: 1.25rem;
  }

  .result {
    padding: 2rem 1rem;
  }

  .success-icon,
  .error-icon {
    width: 70px;
    height: 70px;
    margin-bottom: 1.25rem;
  }

  .success-icon svg,
  .error-icon svg {
    width: 35px;
    height: 35px;
  }

  .result.success h2 {
    font-size: 2rem;
  }

  .result.error h2 {
    font-size: 1.75rem;
  }

  .result p {
    font-size: 0.95rem;
  }
}
</style>
