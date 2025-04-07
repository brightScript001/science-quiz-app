<template>
  <div class="animated-background">
    <div class="gradient-overlay"></div>
    <div class="pattern-overlay"></div>
    <div class="particles">
      <div
        v-for="n in 20"
        :key="n"
        class="particle"
        :style="particleStyle(n)"
      ></div>
    </div>
  </div>
</template>

<script setup>
import { computed } from "vue";

const particleStyle = (n) => {
  const size = Math.floor(Math.random() * 20) + 5;
  const speed = Math.floor(Math.random() * 50) + 20;
  const delay = Math.random() * 5;
  const posX = Math.random() * 100;
  const opacity = Math.random() * 0.5 + 0.1;

  return {
    width: `${size}px`,
    height: `${size}px`,
    left: `${posX}%`,
    animationDuration: `${speed}s`,
    animationDelay: `${delay}s`,
    opacity: opacity,
  };
};
</script>

<style scoped>
.animated-background {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
  overflow: hidden;
}

.gradient-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #0f172a, #1e293b);
  opacity: 1;
}

.pattern-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: url("data:image/svg+xml,%3Csvg width='60' height='60' viewBox='0 0 60 60' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%23ffffff' fill-opacity='0.05'%3E%3Cpath d='M36 34v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zm0-30V0h-2v4h-4v2h4v4h2V6h4V4h-4zM6 34v-4H4v4H0v2h4v4h2v-4h4v-2H6zM6 4V0H4v4H0v2h4v4h2V6h4V4H6z'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
  opacity: 0.5;
}

.particles {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.particle {
  position: absolute;
  bottom: -100px;
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  animation: float linear infinite;
}

@keyframes float {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 0;
  }
  10% {
    opacity: 1;
  }
  90% {
    opacity: 0.5;
  }
  100% {
    transform: translateY(-100vh) rotate(360deg);
    opacity: 0;
  }
}

/* Responsive adjustments */
@media (max-width: 768px) {
  .pattern-overlay {
    opacity: 0.3;
  }
}
</style>
