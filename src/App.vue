<template>
  <div class="quiz-app">
    <AnimatedBackground />

    <div class="quiz-container" :class="currentMode">
      <AppHeader
        :currentLesson="currentLesson"
        :currentScreen="currentScreen"
        :totalScreens="totalScreens"
      />

      <Transition name="fade-slide" mode="out-in">
        <TapScreen
          v-if="currentMode === 'tap'"
          :key="'tap-' + currentScreen"
          :tapScreen="tapScreen"
          @reveal-card="revealCard"
          @next-screen="nextScreen"
        />

        <SwipeScreen
          v-else-if="currentMode === 'swipe'"
          :key="'swipe-' + currentScreen"
          :swipeScreen="swipeScreen"
          @handle-swipe="handleSwipe"
          @next-screen="nextScreen"
        />

        <ResultScreen
          v-else-if="currentMode === 'result'"
          :key="'result-' + currentScreen"
          :isSuccess="isSuccess"
          :resultMessage="resultMessage"
          @try-again="tryAgain"
          @next-screen="nextScreen"
        />
      </Transition>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, watch } from "vue";
import AppHeader from "./components/AppHeader.vue";
import TapScreen from "./views/TapScreen.vue";
import SwipeScreen from "./views/SwipeScreen.vue";
import ResultScreen from "./views/ResultScreen.vue";
import AnimatedBackground from "./components/AnimatedBackground.vue";

// State
const currentLesson = ref("2.1");
const currentScreen = ref(1);
const totalScreens = ref(5);
const currentMode = ref("tap"); // 'tap', 'swipe', 'result'
const isSuccess = ref(true);
const resultMessage = ref("");
const isTransitioning = ref(false);

// Tap screen data
const tapScreen = reactive({
  title: "Tap to Discover States of Matter!",
  cards: [
    {
      label: "Steam",
      description:
        "A gas formed when water is heated to boiling point. It expands to fill any container, with particles moving freely and spread apart.",
      icon: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M12 3v4m0 2v.01M8 6l1 2m6-2l-1 2m-8 3a6 6 0 1 1 12 0c0 3.5-2.5 7-6 8-3.5-1-6-4.5-6-8z"/></svg>',
      color: "teal",
      revealed: false,
    },
    {
      label: "Water",
      description:
        "A liquid takes the shape of its container but keeps its volume. Its particles are close together but can move around each other.",
      icon: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M12 2.69l5.66 5.66a8 8 0 1 1-11.31 0z"/></svg>',
      color: "purple",
      revealed: false,
    },
    {
      label: "Ice",
      description:
        "Fixed shape & volume. Its particles vibrate but don't move freely. They are arranged in a rigid, regular pattern.",
      icon: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5"/></svg>',
      color: "orange",
      revealed: false,
    },
    {
      label: "Lightning",
      description:
        "An electric discharge that occurs during a thunderstorm. It's a form of plasma, which is considered the fourth state of matter.",
      icon: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M13 2L3 14h9l-1 8 10-12h-9l1-8z"/></svg>',
      color: "blue",
      revealed: false,
    },
  ],
});

// Swipe screen data
const swipeScreen = reactive({
  title: "Swipe the cards in the direction of the correct change.",
  leftOption: "Physical Change",
  rightOption: "Chemical Change",
  currentCard: {
    label: "Burning Paper",
    icon: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M12 22c5.5 0 10-4.5 10-10 0-2.5-1-5-3-7l-7 7V2C6.5 2 2 6.5 2 12s4.5 10 10 10z"/></svg>',
    correctAnswer: "right", // 'left' for physical, 'right' for chemical
  },
  cards: [
    {
      label: "Burning Paper",
      icon: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M12 22c5.5 0 10-4.5 10-10 0-2.5-1-5-3-7l-7 7V2C6.5 2 2 6.5 2 12s4.5 10 10 10z"/></svg>',
      correctAnswer: "right",
    },
    {
      label: "Melting Ice",
      icon: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5"/></svg>',
      correctAnswer: "left",
    },
    {
      label: "Rusting Iron",
      icon: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M12 3v19M5 8h14M5 16h14"/></svg>',
      correctAnswer: "right",
    },
  ],
  currentIndex: 0,
});

// Methods for tap screen
const revealCard = (index) => {
  tapScreen.cards[index].revealed = true;
};

const resetTapScreen = () => {
  tapScreen.cards.forEach((card) => {
    card.revealed = false;
  });
};

// Methods for swipe screen
const handleSwipe = (direction) => {
  // Check if the swipe is in the correct direction
  const currentCard = swipeScreen.cards[swipeScreen.currentIndex];

  const isCorrect = direction === currentCard.correctAnswer;

  // Move to next card for next attempt if correct
  if (isCorrect && swipeScreen.currentIndex < swipeScreen.cards.length - 1) {
    setTimeout(() => {
      swipeScreen.currentIndex++;
      swipeScreen.currentCard = swipeScreen.cards[swipeScreen.currentIndex];
    }, 800); // Increased delay to make the transition more obvious
  }

  return isCorrect;
};

// Navigation methods
const nextScreen = () => {
  if (isTransitioning.value) return;
  isTransitioning.value = true;

  setTimeout(() => {
    if (currentMode.value === "result") {
      // Move to next lesson or screen
      if (currentScreen.value < totalScreens.value) {
        currentScreen.value++;

        // Switch between tap and swipe modes
        if (currentScreen.value === 2) {
          currentMode.value = "swipe";
          currentLesson.value = "3.1";
        } else if (currentScreen.value === 4) {
          currentMode.value = "tap";
          resetTapScreen();
        }
      }
    } else if (currentMode.value === "tap") {
      // Check if all cards are revealed before proceeding
      const allRevealed = tapScreen.cards.every((card) => card.revealed);
      if (allRevealed) {
        isSuccess.value = true;
        resultMessage.value =
          "Great job! You've learned about the states of matter.";
        currentMode.value = "result";
      } else {
        // Show error if not all cards are revealed
        isSuccess.value = false;
        resultMessage.value =
          "Please tap all cards to reveal their information.";
        currentMode.value = "result";
      }
    } else if (currentMode.value === "swipe") {
      // In a real app, we would check if all cards have been swiped
      const allSwiped =
        swipeScreen.currentIndex >= swipeScreen.cards.length - 1;
      if (allSwiped) {
        isSuccess.value = true;
        resultMessage.value =
          "Well done! You can now identify physical and chemical changes.";
        currentMode.value = "result";
      } else {
        // Show error if not all cards are swiped
        isSuccess.value = false;
        resultMessage.value =
          "Please swipe all cards to complete this exercise.";
        currentMode.value = "result";
      }
    }

    isTransitioning.value = false;
  }, 300); // Match transition duration
};

const tryAgain = () => {
  if (isTransitioning.value) return;
  isTransitioning.value = true;

  setTimeout(() => {
    if (currentMode.value === "result") {
      if (!isSuccess.value) {
        // Go back to previous screen
        if (currentScreen.value === 2) {
          currentMode.value = "tap";
        } else {
          currentMode.value = "swipe";
        }
      }
    }

    isTransitioning.value = false;
  }, 300); // Match transition duration
};

// Watch for mode changes to add body class for theme
watch(currentMode, (newMode) => {
  document.body.className = `mode-${newMode}`;
  if (newMode === "result") {
    document.body.classList.add(isSuccess.value ? "success" : "error");
  } else {
    document.body.classList.remove("success", "error");
  }
});

// Initialize body class
document.body.className = `mode-${currentMode.value}`;
</script>

<style>
@import "./assets/main.css";

.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

.fade-slide-enter-from {
  opacity: 0;
  transform: translateY(30px);
}

.fade-slide-leave-to {
  opacity: 0;
  transform: translateY(-30px);
}
</style>
