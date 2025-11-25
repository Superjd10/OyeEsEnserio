<template>
  <div class="page-container" @click="toggleHint">
    <button
      v-if="isNextButtonVisible"
      class="next-button"
      @click.stop="nextQuestion"
    >
      →
    </button>
    <div class="content-wrapper">
      <h1 class="main-heading">{{ currentQuestion }}</h1>

      <div class="hint" :class="{ visible: isHintVisible }">
        {{ currentHint }}
      </div>
    </div>

    <p class="copyright">
      <span class="base-text">Oye...</span>
      <span class="animated-line">
        <span class="punctuation opening" :class="{ fading: isFading }">{{
          openingSign
        }}</span>
        <span class="base-text">Es en serio</span>
        <span class="punctuation closing" :class="{ fading: isFading }">{{
          closingSign
        }}</span>
      </span>
    </p>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, computed } from "vue";

const questions = [
  { question: "Question 1", hint: "Hint 1" },
  { question: "Question 2", hint: "Hint 2" },
  { question: "Question 3", hint: "Hint 3" },
  { question: "Question 4", hint: "Hint 4" },
  { question: "Question 5", hint: "Hint 5" },
  { question: "Question 6", hint: "Hint 6" },
  { question: "Question 7", hint: "Hint 7" },
  { question: "Question 8", hint: "Hint 8" },
  { question: "Question 9", hint: "Hint 9" },
  { question: "Question 10", hint: "Hint 10" },
];

// Seeded random number generator
function seededRandom(seed) {
  let state = seed;
  return function () {
    // Simple LCG (Linear Congruential Generator)
    state = (state * 1103515245 + 12345) & 0x7fffffff;
    return state / 0x7fffffff;
  };
}

// Hash function to convert string seed to number
function hashCode(str) {
  let hash = 0;
  for (let i = 0; i < str.length; i++) {
    const char = str.charCodeAt(i);
    hash = (hash << 5) - hash + char;
    hash = hash & hash; // Convert to 32bit integer
  }
  return Math.abs(hash);
}

function shuffleArray(array, seed) {
  const rng = seededRandom(typeof seed === "string" ? hashCode(seed) : seed);
  let currentIndex = array.length;
  let randomIndex;

  // While there remain elements to shuffle.
  while (currentIndex !== 0) {
    // Pick a remaining element using seeded random
    randomIndex = Math.floor(rng() * currentIndex);
    currentIndex--;

    // And swap it with the current element.
    [array[currentIndex], array[randomIndex]] = [
      array[randomIndex],
      array[currentIndex],
    ];
  }

  return array;
}

// Set your seed here (can be a UUID, string, or number)
// For example: "550e8400-e29b-41d4-a716-446655440000" or "my-seed-123"
const seed = "default-seed";

// Create array of indices and shuffle it with seed
const questionIndices = shuffleArray(
  Array.from({ length: questions.length }, (_, i) => i),
  seed
);

const currentQuestionIndex = ref(questionIndices[0]);
const isHintVisible = ref(false);
const openingSign = ref("");
const closingSign = ref("");
const isNextButtonVisible = ref(true);

const currentQuestion = computed(
  () => questions[currentQuestionIndex.value].question
);
const currentHint = computed(() => questions[currentQuestionIndex.value].hint);

const punctuationVariants = [
  { opening: "", closing: "" },
  { opening: "¿", closing: "?" },
  { opening: "¡", closing: "!" },
  { opening: "¡¿", closing: "?!" },
];

let animationTimeout = null;
let isAnimating = false;

const sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));
const isFading = ref(false);

const animateTextChange = async (newVariant) => {
  if (isAnimating) return;
  isAnimating = true;

  const duration = 2000;

  // Fade out punctuation
  isFading.value = true;
  await sleep(duration);

  // Change punctuation while invisible
  openingSign.value = newVariant.opening;
  closingSign.value = newVariant.closing;
  await sleep(100);

  // Fade in punctuation
  isFading.value = false;
  await sleep(duration);

  isAnimating = false;
};

const scheduleNextChange = () => {
  // Random delay between 8-13 seconds
  let times = [1000, 3000];
  const delay = times[0] + Math.random() * times[1];

  animationTimeout = setTimeout(async () => {
    // Pick a random variant different from current
    let newVariant;
    do {
      newVariant =
        punctuationVariants[
          Math.floor(Math.random() * punctuationVariants.length)
        ];
    } while (
      newVariant.opening === openingSign.value &&
      newVariant.closing === closingSign.value
    );

    await animateTextChange(newVariant);
    scheduleNextChange();
  }, delay);
};

const toggleHint = () => {
  isHintVisible.value = !isHintVisible.value;
};

const nextQuestion = () => {
  if (questionIndices.length <= 1) return;

  // Pop the current element
  questionIndices.shift();

  // Update to next question
  currentQuestionIndex.value = questionIndices[0];

  // Hide button if this is the last question
  if (questionIndices.length === 1) {
    isNextButtonVisible.value = false;
  }

  isHintVisible.value = false; // Hide hint when changing question
};

onMounted(() => {
  scheduleNextChange();
});

onUnmounted(() => {
  if (animationTimeout) {
    clearTimeout(animationTimeout);
  }
});
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

:global(html, body) {
  margin: 0;
  padding: 0;
  overflow: hidden;
  height: 100vh;
  height: 100dvh;
  width: 100vw;
  position: fixed;
  -webkit-tap-highlight-color: transparent;
  -webkit-touch-callout: none;
  -webkit-user-select: none;
  user-select: none;
  touch-action: manipulation;
}

.page-container {
  height: 100vh;
  height: 100dvh;
  width: 100vw;
  background-color: #6b2c2c;
  background-image: radial-gradient(
      circle at 20% 30%,
      rgba(139, 69, 69, 0.3) 0%,
      transparent 50%
    ),
    radial-gradient(
      circle at 80% 70%,
      rgba(139, 69, 69, 0.3) 0%,
      transparent 50%
    ),
    radial-gradient(
      circle at 40% 80%,
      rgba(101, 44, 44, 0.4) 0%,
      transparent 40%
    );
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: fixed;
  top: 0;
  left: 0;
  overflow: hidden;
  padding: 0;
  margin: 0;
  cursor: pointer;
  -webkit-tap-highlight-color: transparent;
  -webkit-touch-callout: none;
  touch-action: manipulation;
}

.page-container::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-image: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 2px,
    rgba(0, 0, 0, 0.03) 2px,
    rgba(0, 0, 0, 0.03) 4px
  );
  pointer-events: none;
}

.content-wrapper {
  text-align: center;
  color: #f5e6d3;
  max-width: 90vw;
  z-index: 1;
  padding: 2rem 2rem 6rem 2rem;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 100%;
}

.main-heading {
  font-family: "Bodoni Moda", "Didot", "Playfair Display", Georgia, serif;
  font-size: clamp(2rem, 5vw, 4.5rem);
  font-weight: 400;
  letter-spacing: 0.15em;
  line-height: 1.3;
  margin-bottom: clamp(2rem, 5vh, 4rem);
  text-transform: uppercase;
  color: #f5e6d3;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}

.hint {
  border: 3px solid #f5e6d3;
  padding: clamp(1.5rem, 3vh, 2rem) clamp(2rem, 4vw, 3rem);
  margin: 0 auto;
  max-width: 700px;
  font-family: "Bodoni Moda", "Didot", "Playfair Display", Georgia, serif;
  font-size: clamp(1rem, 2vw, 1.6rem);
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: #f5e6d3;
  background: rgba(0, 0, 0, 0.1);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
  opacity: 0;
  max-height: 0;
  overflow: hidden;
  transform: translateY(-20px);
  transition: all 0.4s ease;
}

.hint.visible {
  opacity: 1;
  max-height: 500px;
  transform: translateY(0);
}

.copyright {
  font-family: "Bodoni Moda", "Didot", "Playfair Display", Georgia, serif;
  font-size: clamp(2rem, 3vw, 3rem);
  font-weight: 300;
  font-style: italic;
  letter-spacing: 0.05em;
  color: #f5e6d3;
  text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.3);
  position: fixed;
  bottom: 1.8rem;
  left: 50%;
  transform: translateX(-50%);
  z-index: 10;
  margin: 0;
  text-align: center;
  white-space: nowrap;
  pointer-events: none;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 0.3rem;
}

.base-text {
  opacity: 1;
}

.punctuation {
  opacity: 1;
  transition: opacity 2s ease-in-out;
  display: inline-block;
  min-width: 0.1em;
}

.punctuation.fading {
  opacity: 0;
}

.next-button {
  position: fixed;
  top: 2rem;
  right: 2rem;
  width: 60px;
  height: 60px;
  border: 3px solid #f5e6d3;
  background: rgba(0, 0, 0, 0.2);
  color: #f5e6d3;
  font-size: 2rem;
  font-weight: 300;
  cursor: pointer;
  z-index: 100;
  pointer-events: auto;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
  -webkit-tap-highlight-color: transparent;
  -webkit-touch-callout: none;
  user-select: none;
}

.next-button:hover {
  background: rgba(0, 0, 0, 0.4);
  transform: scale(1.1);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.4);
}

.next-button:active {
  transform: scale(0.95);
}

@media (max-width: 768px) {
  .main-heading {
    margin-bottom: 2.5rem;
    letter-spacing: 0.1em;
  }

  .hint {
    padding: 1.5rem 2rem;
    letter-spacing: 0.05em;
  }

  .content-wrapper {
    padding: 2rem 2rem 5rem 2rem;
  }

  .copyright {
    bottom: 2rem;
    font-size: clamp(0.85rem, 3.5vw, 1.1rem);
  }

  .next-button {
    width: 50px;
    height: 50px;
    top: 1.5rem;
    right: 1.5rem;
    font-size: 1.5rem;
  }
}

@media (max-width: 1024px) and (min-width: 769px) {
  .content-wrapper {
    padding: 2rem 2rem 5.5rem 2rem;
  }

  .copyright {
    bottom: 1rem;
    font-size: clamp(0.95rem, 2.2vw, 1.6rem);
  }
}

/* iPad landscape and similar */
@media (max-height: 600px) and (orientation: landscape) {
  .main-heading {
    font-size: clamp(1.5rem, 4vw, 3rem);
    margin-bottom: clamp(1rem, 3vh, 2rem);
  }

  .hint {
    padding: clamp(1rem, 2vh, 1.5rem) clamp(1.5rem, 3vw, 2.5rem);
    font-size: clamp(0.9rem, 1.8vw, 1.3rem);
  }

  .content-wrapper {
    padding: 1rem 2rem 4rem 2rem;
  }

  .copyright {
    bottom: 0.8rem;
    font-size: clamp(0.75rem, 1.5vw, 1.2rem);
  }
}
</style>
