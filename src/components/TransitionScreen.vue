<template>
  <div class="transition-screen">
    <!-- Animated Background -->
    <div class="bg-animation">
      <div class="wave wave-1"></div>
      <div class="wave wave-2"></div>
      <div class="wave wave-3"></div>
    </div>

    <!-- Floating Particles -->
    <div class="particles-container">
      <div v-for="n in 30" :key="n" class="particle" :style="getParticleStyle(n)"></div>
    </div>

    <!-- Main Content -->
    <div class="content">
      <!-- Animated Logo -->
      <div class="logo-animation">
        <div class="ring ring-outer">
          <div class="ring-segment" v-for="n in 8" :key="n" :style="getSegmentStyle(n)"></div>
        </div>
        <div class="ring ring-middle">
          <div class="ring-segment" v-for="n in 6" :key="n" :style="getSegmentStyle(n, 60)"></div>
        </div>
        <div class="ring ring-inner">
          <div class="center-gem">
            <div class="gem-facet" v-for="n in 6" :key="n"></div>
          </div>
        </div>
      </div>

      <!-- Welcome Message -->
      <h1 class="welcome-title">
        <span class="title-word" v-for="(word, index) in titleWords" :key="index" :style="getWordStyle(index)">
          {{ word }}
        </span>
      </h1>

      <p class="subtitle">{{ currentMessage }}</p>

      <!-- Progress Container -->
      <div class="progress-container">
        <div class="progress-bar">
          <div class="progress-fill" :style="{ width: progress + '%' }">
            <div class="progress-shine"></div>
          </div>
          <div class="progress-glow" :style="{ width: progress + '%' }"></div>
        </div>
        <div class="progress-percentage">{{ progress }}%</div>
      </div>

      <!-- Loading Status -->
      <div class="loading-status">
        <div class="status-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <path d="M12 2L2 7l10 5 10-5-10-5z"></path>
            <path d="M2 17l10 5 10-5"></path>
            <path d="M2 12l10 5 10-5"></path>
          </svg>
        </div>
        <p class="status-text">{{ loadingText }}</p>
      </div>

      <!-- Success Checkmark (appears at end) -->
      <transition name="checkmark-appear">
        <div v-if="progress >= 100" class="success-checkmark">
          <svg viewBox="0 0 52 52">
            <circle class="checkmark-circle" cx="26" cy="26" r="25" fill="none"/>
            <path class="checkmark-check" fill="none" d="M14.1 27.2l7.1 7.2 16.7-16.8"/>
          </svg>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TransitionScreen',
  data() {
    return {
      progress: 0,
      loadingText: 'Initializing...',
      currentMessage: 'Registration successful. Preparing your premium gold calculation experience...',
      titleWords: ['Welcome', 'to', 'Prima', 'Gold!'],
      texts: [
        'Initializing system...',
        'Loading gold market data...',
        'Preparing calculator interface...',
        'Finalizing setup...',
        'Ready!'
      ]
    }
  },
  mounted() {
    this.startTransition()
  },
  methods: {
    getParticleStyle() {
      const size = Math.random() * 4 + 2
      return {
        width: `${size}px`,
        height: `${size}px`,
        left: `${Math.random() * 100}%`,
        top: `${Math.random() * 100}%`,
        animationDelay: `${Math.random() * 3}s`,
        animationDuration: `${3 + Math.random() * 4}s`
      }
    },
    getSegmentStyle(n, offset = 0) {
      const angle = (360 / 8) * n + offset
      return {
        transform: `rotate(${angle}deg)`
      }
    },
    getWordStyle(index) {
      return {
        animationDelay: `${index * 0.1}s`
      }
    },
    startTransition() {
      const duration = 3000 // 3 seconds
      const steps = 100
      const interval = duration / steps
      let currentStep = 0

      const timer = setInterval(() => {
        currentStep++
        this.progress = currentStep

        // Update loading text based on progress
        const textIndex = Math.floor((currentStep / steps) * this.texts.length)
        this.loadingText = this.texts[Math.min(textIndex, this.texts.length - 1)]

        if (currentStep >= steps) {
          clearInterval(timer)
          // Wait a bit to show the success checkmark before transitioning
          setTimeout(() => {
            this.$emit('transition-complete')
          }, 800)
        }
      }, interval)
    }
  }
}
</script>

<style scoped>
.transition-screen {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  background: linear-gradient(135deg, #0f0f1e 0%, #1a1a2e 50%, #16213e 100%);
  color: white;
  position: relative;
  overflow: hidden;
}

/* Background Animation */
.bg-animation {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
  z-index: 0;
}

.wave {
  position: absolute;
  width: 200%;
  height: 200%;
  background: radial-gradient(ellipse at center, rgba(255, 215, 0, 0.1), transparent 70%);
  animation: waveAnimation 15s infinite ease-in-out;
}

.wave-1 {
  top: -100%;
  left: -50%;
  animation-duration: 12s;
}

.wave-2 {
  bottom: -100%;
  right: -50%;
  animation-duration: 18s;
  animation-delay: 2s;
}

.wave-3 {
  top: 0;
  left: 0;
  animation-duration: 20s;
  animation-delay: 4s;
}

@keyframes waveAnimation {
  0%, 100% { transform: translate(0, 0) scale(1); opacity: 0.3; }
  50% { transform: translate(-10%, -10%) scale(1.1); opacity: 0.5; }
}

/* Particles */
.particles-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
  z-index: 0;
}

.particle {
  position: absolute;
  background: radial-gradient(circle, #ffd700, transparent);
  border-radius: 50%;
  animation: particleFloat 6s infinite ease-in-out;
  opacity: 0;
}

@keyframes particleFloat {
  0%, 100% {
    opacity: 0;
    transform: translateY(0) scale(1);
  }
  10%, 90% {
    opacity: 0.8;
  }
  50% {
    opacity: 1;
    transform: translateY(-50vh) scale(1.5);
  }
}

/* Main Content */
.content {
  text-align: center;
  max-width: 600px;
  padding: 60px 40px;
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.05), rgba(255, 255, 255, 0.02));
  border-radius: 30px;
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 215, 0, 0.2);
  box-shadow: 
    0 30px 60px rgba(0, 0, 0, 0.5),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
  position: relative;
  z-index: 1;
  animation: contentAppear 1s cubic-bezier(0.34, 1.56, 0.64, 1);
}

@keyframes contentAppear {
  from {
    opacity: 0;
    transform: scale(0.8) translateY(50px);
  }
  to {
    opacity: 1;
    transform: scale(1) translateY(0);
  }
}

/* Logo Animation */
.logo-animation {
  position: relative;
  width: 150px;
  height: 150px;
  margin: 0 auto 40px;
}

.ring {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  border-radius: 50%;
}

.ring-outer {
  width: 150px;
  height: 150px;
  animation: rotateClockwise 10s linear infinite;
}

.ring-middle {
  width: 100px;
  height: 100px;
  animation: rotateCounterClockwise 8s linear infinite;
}

.ring-inner {
  width: 60px;
  height: 60px;
  display: flex;
  align-items: center;
  justify-content: center;
}

@keyframes rotateClockwise {
  from { transform: translate(-50%, -50%) rotate(0deg); }
  to { transform: translate(-50%, -50%) rotate(360deg); }
}

@keyframes rotateCounterClockwise {
  from { transform: translate(-50%, -50%) rotate(0deg); }
  to { transform: translate(-50%, -50%) rotate(-360deg); }
}

.ring-segment {
  position: absolute;
  top: 0;
  left: 50%;
  width: 4px;
  height: 20px;
  background: linear-gradient(180deg, #ffd700, transparent);
  border-radius: 2px;
  transform-origin: center 75px;
  box-shadow: 0 0 10px rgba(255, 215, 0, 0.5);
}

.ring-middle .ring-segment {
  height: 15px;
  transform-origin: center 50px;
}

.center-gem {
  width: 40px;
  height: 40px;
  position: relative;
  animation: gemPulse 2s ease-in-out infinite;
}

@keyframes gemPulse {
  0%, 100% {
    transform: scale(1);
    filter: drop-shadow(0 0 10px rgba(255, 215, 0, 0.5));
  }
  50% {
    transform: scale(1.2);
    filter: drop-shadow(0 0 20px rgba(255, 215, 0, 0.8));
  }
}

.gem-facet {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #ffd700 0%, #ffed4e 50%, #ffd700 100%);
  clip-path: polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%);
  animation: facetRotate 3s linear infinite;
}

.gem-facet:nth-child(2) { animation-delay: 0.5s; opacity: 0.8; }
.gem-facet:nth-child(3) { animation-delay: 1s; opacity: 0.6; }
.gem-facet:nth-child(4) { animation-delay: 1.5s; opacity: 0.4; }
.gem-facet:nth-child(5) { animation-delay: 2s; opacity: 0.3; }
.gem-facet:nth-child(6) { animation-delay: 2.5s; opacity: 0.2; }

@keyframes facetRotate {
  from { transform: translate(-50%, -50%) rotate(0deg); }
  to { transform: translate(-50%, -50%) rotate(60deg); }
}

/* Welcome Title */
.welcome-title {
  font-size: 3rem;
  margin-bottom: 20px;
  font-weight: 900;
  line-height: 1.2;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 12px;
}

.title-word {
  display: inline-block;
  background: linear-gradient(135deg, #ffd700 0%, #ffed4e 50%, #ffd700 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  animation: wordAppear 0.6s cubic-bezier(0.34, 1.56, 0.64, 1) both;
  text-shadow: 0 0 30px rgba(255, 215, 0, 0.3);
}

@keyframes wordAppear {
  from {
    opacity: 0;
    transform: translateY(20px) rotateX(-90deg);
  }
  to {
    opacity: 1;
    transform: translateY(0) rotateX(0);
  }
}

.subtitle {
  font-size: 1.15rem;
  margin-bottom: 40px;
  color: rgba(255, 255, 255, 0.8);
  line-height: 1.6;
  font-weight: 500;
  animation: fadeIn 1s ease 0.5s both;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* Progress Container */
.progress-container {
  position: relative;
  margin-bottom: 30px;
  animation: fadeIn 1s ease 0.7s both;
}

.progress-bar {
  width: 100%;
  height: 12px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 20px;
  overflow: hidden;
  position: relative;
  box-shadow: inset 0 2px 8px rgba(0, 0, 0, 0.3);
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #ffd700 0%, #ffed4e 50%, #ffd700 100%);
  background-size: 200% 100%;
  border-radius: 20px;
  transition: width 0.3s ease;
  position: relative;
  animation: progressShine 2s linear infinite;
  box-shadow: 
    0 0 20px rgba(255, 215, 0, 0.5),
    inset 0 1px 0 rgba(255, 255, 255, 0.3);
}

@keyframes progressShine {
  0% { background-position: 0% 50%; }
  100% { background-position: 200% 50%; }
}

.progress-shine {
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.5), transparent);
  animation: shineMove 1.5s infinite;
}

@keyframes shineMove {
  0% { left: -100%; }
  100% { left: 200%; }
}

.progress-glow {
  position: absolute;
  top: 50%;
  left: 0;
  height: 20px;
  background: radial-gradient(ellipse at center, rgba(255, 215, 0, 0.6), transparent);
  transform: translateY(-50%);
  filter: blur(8px);
  transition: width 0.3s ease;
}

.progress-percentage {
  position: absolute;
  top: -35px;
  right: 0;
  font-size: 1.5rem;
  font-weight: 800;
  color: #ffd700;
  text-shadow: 0 0 20px rgba(255, 215, 0, 0.5);
  animation: numberCount 0.3s ease;
}

/* Loading Status */
.loading-status {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
  animation: fadeIn 1s ease 0.9s both;
}

.status-icon {
  width: 30px;
  height: 30px;
  animation: iconSpin 2s linear infinite;
}

.status-icon svg {
  width: 100%;
  height: 100%;
  stroke: #ffd700;
  filter: drop-shadow(0 0 10px rgba(255, 215, 0, 0.5));
}

@keyframes iconSpin {
  from { transform: rotateY(0deg); }
  to { transform: rotateY(360deg); }
}

.status-text {
  font-size: 1.2rem;
  color: #ffed4e;
  font-weight: 600;
  letter-spacing: 0.5px;
}

/* Success Checkmark */
.checkmark-appear-enter-active {
  animation: checkmarkAppear 0.8s cubic-bezier(0.34, 1.56, 0.64, 1);
}

@keyframes checkmarkAppear {
  from {
    opacity: 0;
    transform: scale(0) rotate(-180deg);
  }
  to {
    opacity: 1;
    transform: scale(1) rotate(0);
  }
}

.success-checkmark {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 120px;
  height: 120px;
  z-index: 10;
}

.success-checkmark svg {
  width: 100%;
  height: 100%;
}

.checkmark-circle {
  stroke: #2ecc71;
  stroke-width: 3;
  stroke-dasharray: 166;
  stroke-dashoffset: 166;
  animation: strokeCircle 0.6s cubic-bezier(0.65, 0, 0.45, 1) forwards;
  filter: drop-shadow(0 0 20px rgba(46, 213, 115, 0.6));
}

@keyframes strokeCircle {
  to {
    stroke-dashoffset: 0;
  }
}

.checkmark-check {
  stroke: #2ecc71;
  stroke-width: 3;
  stroke-linecap: round;
  stroke-dasharray: 48;
  stroke-dashoffset: 48;
  animation: strokeCheck 0.3s cubic-bezier(0.65, 0, 0.45, 1) 0.6s forwards;
  filter: drop-shadow(0 0 20px rgba(46, 213, 115, 0.6));
}

@keyframes strokeCheck {
  to {
    stroke-dashoffset: 0;
  }
}

/* Responsive Design */
@media (max-width: 768px) {
  .content {
    padding: 40px 30px;
    max-width: 90%;
  }

  .logo-animation {
    width: 120px;
    height: 120px;
    margin-bottom: 30px;
  }

  .ring-outer {
    width: 120px;
    height: 120px;
  }

  .ring-middle {
    width: 80px;
    height: 80px;
  }

  .ring-segment {
    transform-origin: center 60px;
  }

  .ring-middle .ring-segment {
    transform-origin: center 40px;
  }

  .welcome-title {
    font-size: 2rem;
    gap: 8px;
  }

  .subtitle {
    font-size: 1rem;
  }

  .progress-percentage {
    font-size: 1.2rem;
    top: -30px;
  }

  .status-text {
    font-size: 1rem;
  }

  .success-checkmark {
    width: 100px;
    height: 100px;
  }
}
</style>