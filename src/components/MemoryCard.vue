<template>
  <div class="card" :class="{ flipped, matched }" @click="$emit('click')" :style="cardStyle">
    <div class="inner">
      <div class="front">
        <div class="icon">{{ card.icon }}</div>
      </div>
      <div class="back">
        <div class="pattern">❓</div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps(['card', 'flipped', 'matched', 'style'])

const cardStyle = computed(() => ({
  animationDelay: props.style?.animationDelay || '0s'
}))
</script>

<style scoped>
.card {
  width: 100px;
  height: 100px;
  perspective: 1000px;
  cursor: pointer;
  transition: transform 0.2s ease;
  animation: cardAppear 0.6s ease-out both;
}

.card:hover {
  transform: scale(1.05);
}

.inner {
  width: 100%;
  height: 100%;
  transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
  transform-style: preserve-3d;
  position: relative;
}

.flipped .inner, .matched .inner {
  transform: rotateY(180deg);
}

.front, .back {
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 15px;
  backface-visibility: hidden;
  box-shadow: 0 8px 25px rgba(0,0,0,0.15);
  transition: all 0.3s ease;
}

.back {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border: 3px solid rgba(255,255,255,0.3);
}

.back .pattern {
  font-size: 35px;
  color: white;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
  animation: pulse 2s infinite;
}

.front {
  background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 100%);
  border: 3px solid rgba(255,255,255,0.5);
  transform: rotateY(180deg);
}

.front .icon {
  font-size: 40px;
  filter: drop-shadow(2px 2px 4px rgba(0,0,0,0.2));
  animation: bounce 0.6s ease-out;
}

.matched .front {
  background: linear-gradient(135deg, #a8edea 0%, #fed6e3 100%);
  border-color: #4CAF50;
  animation: matchedGlow 0.6s ease-out;
}

.matched .icon {
  animation: celebration 0.8s ease-out;
}

@keyframes pulse {
  0%, 100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
}

@keyframes bounce {
  0% {
    transform: scale(0.3);
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
  }
}

@keyframes matchedGlow {
  0% {
    box-shadow: 0 8px 25px rgba(0,0,0,0.15);
  }
  50% {
    box-shadow: 0 8px 25px rgba(76, 175, 80, 0.6), 0 0 20px rgba(76, 175, 80, 0.4);
  }
  100% {
    box-shadow: 0 8px 25px rgba(0,0,0,0.15);
  }
}

@keyframes celebration {
  0% {
    transform: scale(1) rotate(0deg);
  }
  25% {
    transform: scale(1.2) rotate(-5deg);
  }
  50% {
    transform: scale(1.1) rotate(5deg);
  }
  75% {
    transform: scale(1.15) rotate(-3deg);
  }
  100% {
    transform: scale(1) rotate(0deg);
  }
}

@keyframes cardAppear {
  from {
    opacity: 0;
    transform: scale(0.5) rotateY(180deg);
  }
  to {
    opacity: 1;
    transform: scale(1) rotateY(0deg);
  }
}
</style>
