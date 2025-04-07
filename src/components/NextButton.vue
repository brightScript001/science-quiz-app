<template>
  <button
    class="btn-next"
    :class="{ 'btn-primary': primary, 'btn-secondary': !primary }"
    @click="$emit('click')"
  >
    <span class="btn-content">
      <slot>Next</slot>
      <span class="btn-icon">
        <svg
          v-if="icon === 'arrow'"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <line x1="5" y1="12" x2="19" y2="12"></line>
          <polyline points="12 5 19 12 12 19"></polyline>
        </svg>
        <svg
          v-else-if="icon === 'refresh'"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <path d="M23 4v6h-6"></path>
          <path d="M1 20v-6h6"></path>
          <path d="M3.51 9a9 9 0 0 1 14.85-3.36L23 10"></path>
          <path d="M20.49 15a9 9 0 0 1-14.85 3.36L1 14"></path>
        </svg>
      </span>
    </span>
    <span class="btn-background"></span>
  </button>
</template>

<script setup>
defineProps({
  primary: {
    type: Boolean,
    default: true,
  },
  icon: {
    type: String,
    default: "arrow",
  },
});

defineEmits(["click"]);
</script>

<style scoped>
.btn-next {
  position: relative;
  border: none;
  border-radius: 12px;
  padding: 0;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  overflow: hidden;
  transition: all 0.3s ease;
  margin-top: auto;
  height: 56px;
  width: 100%;
}

.btn-content {
  position: relative;
  z-index: 2;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  padding: 0 1.5rem;
  transition: all 0.3s ease;
}

.btn-background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  transition: all 0.3s ease;
}

.btn-primary .btn-background {
  background: linear-gradient(135deg, #3b82f6, #8b5cf6);
}

.btn-secondary .btn-background {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
}

.btn-primary {
  color: white;
  box-shadow: 0 8px 20px rgba(59, 130, 246, 0.3);
}

.btn-secondary {
  color: white;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
}

.btn-next:hover {
  transform: translateY(-2px);
}

.btn-primary:hover .btn-background {
  background: linear-gradient(135deg, #2563eb, #7c3aed);
}

.btn-secondary:hover .btn-background {
  background: rgba(255, 255, 255, 0.15);
}

.btn-next:active {
  transform: translateY(0);
}

.btn-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-left: 0.75rem;
  width: 20px;
  height: 20px;
}

.btn-icon svg {
  width: 100%;
  height: 100%;
}

@media (max-width: 480px) {
  .btn-next {
    height: 50px;
    font-size: 0.95rem;
  }
}
</style>
