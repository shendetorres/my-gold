<template>
  <div class="gold-calculator">
    <!-- Animated Background Elements -->
    <div class="bg-decoration">
      <div class="floating-circle circle-1"></div>
      <div class="floating-circle circle-2"></div>
      <div class="floating-circle circle-3"></div>
      <div class="gold-particles">
        <span v-for="n in 20" :key="n" class="particle" :style="getParticleStyle(n)"></span>
      </div>
    </div>

    <div class="header">
      <div class="logo-section">
        <div class="logo-icon">
          <div class="gold-ring">
            <div class="inner-ring"></div>
          </div>
        </div>
        <h1>Prima Gold Calculator</h1>
      </div>
      <button @click="logout" class="logout-btn" title="Logout and return to registration">
        <span class="logout-icon">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
            <path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"></path>
            <polyline points="16 17 21 12 16 7"></polyline>
            <line x1="21" y1="12" x2="9" y2="12"></line>
          </svg>
        </span>
        <span class="logout-text">Logout</span>
      </button>
    </div>

    <div class="karat-tabs">
      <button
        v-for="karat in karats"
        :key="karat.karat"
        @click="selectKarat(karat.karat)"
        :class="['karat-tab', { active: selectedKarat === karat.karat }]"
      >
        <span class="karat-value">{{ karat.karat }}K</span>
        <span class="karat-shine"></span>
      </button>
    </div>

    <div v-if="selectedKarat" class="karat-calculator">
      <div class="karat-header">
        <div class="header-content">
          <h2>{{ selectedKarat }}K Gold Calculator</h2>
          <div class="purity-info">
            <div class="purity-badge">
              <span class="badge-icon">✨</span>
              <span class="badge-text">{{ getKaratInfo(selectedKarat).purity }}% Pure Gold</span>
            </div>
          </div>
        </div>
      </div>

      <div class="calculation-section">
        <div class="input-group">
          <div class="input-field" :class="{ error: errors.goldRate, focused: focusedField === 'goldRate' }">
            <label>
              <span class="label-icon">💰</span>
              Gold Rate per Gram
            </label>
            <div class="input-wrapper">
              <span class="currency">₱</span>
              <input 
                type="number" 
                v-model.number="goldRate" 
                step="0.01" 
                placeholder="0.00"
                @focus="focusedField = 'goldRate'"
                @blur="focusedField = null"
              >
              <div class="input-underline"></div>
            </div>
            <transition name="error-slide">
              <div v-if="errors.goldRate" class="error-message">
                <span class="error-icon">⚠️</span>
                {{ errors.goldRate }}
              </div>
            </transition>
          </div>

          <div class="input-field" :class="{ error: errors.grams, focused: focusedField === 'grams' }">
            <label>
              <span class="label-icon">⚖️</span>
              Weight
            </label>
            <div class="input-wrapper">
              <input 
                type="number" 
                v-model.number="grams" 
                step="0.01" 
                placeholder="0.00"
                @focus="focusedField = 'grams'"
                @blur="focusedField = null"
              >
              <span class="unit">grams</span>
              <div class="input-underline"></div>
            </div>
            <transition name="error-slide">
              <div v-if="errors.grams" class="error-message">
                <span class="error-icon">⚠️</span>
                {{ errors.grams }}
              </div>
            </transition>
          </div>

          <div class="input-field" :class="{ error: errors.makingCharge, focused: focusedField === 'makingCharge' }">
            <label>
              <span class="label-icon">🔨</span>
              Making Charge
            </label>
            <div class="input-wrapper">
              <span class="currency">₱</span>
              <input 
                type="number" 
                v-model.number="makingCharge" 
                step="0.01" 
                placeholder="0.00"
                @focus="focusedField = 'makingCharge'"
                @blur="focusedField = null"
              >
              <div class="input-underline"></div>
            </div>
            <transition name="error-slide">
              <div v-if="errors.makingCharge" class="error-message">
                <span class="error-icon">⚠️</span>
                {{ errors.makingCharge }}
              </div>
            </transition>
          </div>
        </div>

        <button @click="calculateForKarat(selectedKarat)" class="calculate-btn">
          <span class="btn-background"></span>
          <span class="btn-content">
            <span class="btn-text">Calculate {{ selectedKarat }}K Price</span>
            <span class="btn-icon">
              <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M12 2L2 7l10 5 10-5-10-5z"></path>
                <path d="M2 17l10 5 10-5"></path>
                <path d="M2 12l10 5 10-5"></path>
              </svg>
            </span>
          </span>
          <span class="btn-ripple"></span>
        </button>
      </div>

      <transition name="result-appear">
        <div v-if="currentResult" class="result-display">
          <div class="result-header">
            <h3>
              <span class="header-icon">📊</span>
              Price Breakdown
            </h3>
            <div class="result-decoration"></div>
          </div>
          
          <div class="calculation-breakdown">
            <div class="calc-item">
              <span class="label">
                <span class="label-dot"></span>
                Gold Purity
              </span>
              <span class="value">{{ currentResult.purity }}%</span>
            </div>
            <div class="calc-item">
              <span class="label">
                <span class="label-dot"></span>
                Adjusted Rate ({{ selectedKarat }}K)
              </span>
              <span class="value">₱{{ (goldRate * (currentResult.purity / 100)).toFixed(2) }}</span>
            </div>
            <div class="calc-item">
              <span class="label">
                <span class="label-dot"></span>
                Gold Value
              </span>
              <span class="value">₱{{ currentResult.goldValue.toFixed(2) }}</span>
            </div>
            <div class="calc-item">
              <span class="label">
                <span class="label-dot"></span>
                Making Charge
              </span>
              <span class="value">₱{{ makingCharge.toFixed(2) }}</span>
            </div>
            <div class="calc-item subtotal">
              <span class="label">
                <span class="label-dot"></span>
                Subtotal
              </span>
              <span class="value">₱{{ currentResult.subtotal.toFixed(2) }}</span>
            </div>
            <div class="calc-item tax">
              <span class="label">
                <span class="label-dot"></span>
                Tax (12%)
              </span>
              <span class="value">₱{{ currentResult.tax.toFixed(2) }}</span>
            </div>
            <div class="calc-item total">
              <span class="label">
                <span class="label-dot gold"></span>
                <strong>Total Price</strong>
              </span>
              <span class="value">
                <span class="total-amount">₱{{ currentResult.total.toFixed(2) }}</span>
              </span>
            </div>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GoldCalculator',
  data() {
    return {
      goldRate: null,
      grams: null,
      makingCharge: null,
      selectedKarat: null,
      currentResult: null,
      focusedField: null,
      errors: {
        goldRate: '',
        grams: '',
        makingCharge: ''
      },
      karats: [
        { karat: 10, purity: 100 },
        { karat: 14, purity: 100 },
        { karat: 16, purity: 100 },
        { karat: 18, purity: 100 },
        { karat: 20, purity: 100 },
        { karat: 21, purity: 100 },
        { karat: 22, purity: 100 },
        { karat: 23, purity: 100 },
        { karat: 24, purity: 100 },
        { karat: 25, purity: 100 }
      ]
    }
  },
  methods: {
    getParticleStyle() {
      return {
        left: `${Math.random() * 100}%`,
        animationDelay: `${Math.random() * 5}s`,
        animationDuration: `${5 + Math.random() * 10}s`
      }
    },
    selectKarat(karat) {
      this.goldRate = null
      this.grams = null
      this.makingCharge = null
      this.currentResult = null
      this.focusedField = null
      this.errors = {
        goldRate: '',
        grams: '',
        makingCharge: ''
      }
      this.selectedKarat = karat
    },
    validateInputs() {
      this.errors.goldRate = ''
      this.errors.grams = ''
      this.errors.makingCharge = ''

      if (!this.goldRate || this.goldRate <= 0 || isNaN(this.goldRate)) {
        this.errors.goldRate = 'Please enter a valid gold rate greater than 0'
      }
      if (!this.grams || this.grams <= 0 || isNaN(this.grams)) {
        this.errors.grams = 'Please enter a valid weight greater than 0'
      }
      if (this.makingCharge === null || this.makingCharge === '' || isNaN(this.makingCharge) || this.makingCharge < 0) {
        this.errors.makingCharge = 'Please enter a valid making charge (0 or greater)'
      }

      return !this.errors.goldRate && !this.errors.grams && !this.errors.makingCharge
    },
    calculateForKarat(karat) {
      if (!this.validateInputs()) {
        return
      }

      const karatInfo = this.getKaratInfo(karat)
      const purity = karatInfo.purity / 100
      const adjustedRate = this.goldRate * purity
      const goldValue = adjustedRate * this.grams
      const subtotal = goldValue + this.makingCharge
      const tax = subtotal * 0.12
      const total = subtotal + tax

      this.currentResult = {
        karat: karat,
        purity: karatInfo.purity,
        goldValue: goldValue,
        subtotal: subtotal,
        tax: tax,
        total: total
      }
    },
    getKaratInfo(karat) {
      return this.karats.find(k => k.karat === karat)
    },
    logout() {
      if (confirm('Are you sure you want to logout and return to registration?')) {
        this.$emit('logout')
      }
    }
  }
}
</script>

<style scoped>
.gold-calculator {
  background: linear-gradient(135deg, #0f0f1e 0%, #1a1a2e 50%, #16213e 100%);
  border-radius: 30px;
  padding: 50px;
  box-shadow: 
    0 30px 60px rgba(0, 0, 0, 0.5),
    0 0 0 1px rgba(255, 215, 0, 0.1),
    inset 0 1px 0 rgba(255, 255, 255, 0.05);
  max-width: 1100px;
  width: 100%;
  position: relative;
  overflow: hidden;
  animation: fadeInScale 0.8s cubic-bezier(0.34, 1.56, 0.64, 1);
}

@keyframes fadeInScale {
  from {
    opacity: 0;
    transform: scale(0.9) translateY(30px);
  }
  to {
    opacity: 1;
    transform: scale(1) translateY(0);
  }
}

/* Background Decorations */
.bg-decoration {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  overflow: hidden;
  z-index: 0;
}

.floating-circle {
  position: absolute;
  border-radius: 50%;
  background: radial-gradient(circle, rgba(255, 215, 0, 0.15), transparent);
  animation: float 20s infinite ease-in-out;
}

.circle-1 {
  width: 400px;
  height: 400px;
  top: -200px;
  right: -200px;
  animation-duration: 25s;
}

.circle-2 {
  width: 300px;
  height: 300px;
  bottom: -150px;
  left: -150px;
  animation-duration: 30s;
  animation-delay: 5s;
}

.circle-3 {
  width: 250px;
  height: 250px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  animation-duration: 35s;
  animation-delay: 10s;
}

@keyframes float {
  0%, 100% { transform: translate(0, 0) scale(1); }
  25% { transform: translate(20px, -20px) scale(1.1); }
  50% { transform: translate(-15px, 15px) scale(0.9); }
  75% { transform: translate(15px, 20px) scale(1.05); }
}

.gold-particles {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}

.particle {
  position: absolute;
  width: 3px;
  height: 3px;
  background: radial-gradient(circle, #ffd700, transparent);
  border-radius: 50%;
  animation: particleFloat 15s infinite linear;
  opacity: 0;
}

@keyframes particleFloat {
  0% {
    opacity: 0;
    transform: translateY(100vh) rotate(0deg);
  }
  10% {
    opacity: 1;
  }
  90% {
    opacity: 1;
  }
  100% {
    opacity: 0;
    transform: translateY(-100px) rotate(360deg);
  }
}

/* Header */
.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 50px;
  position: relative;
  z-index: 1;
}

.logo-section {
  display: flex;
  align-items: center;
  gap: 20px;
  flex: 1;
  justify-content: center;
}

.logo-icon {
  position: relative;
  width: 70px;
  height: 70px;
}

.gold-ring {
  width: 100%;
  height: 100%;
  border: 4px solid;
  border-image: linear-gradient(45deg, #ffd700, #ffed4e, #b8860b, #ffd700) 1;
  border-radius: 50%;
  animation: rotate 10s linear infinite;
  position: relative;
  box-shadow: 
    0 0 20px rgba(255, 215, 0, 0.3),
    inset 0 0 20px rgba(255, 215, 0, 0.1);
}

.inner-ring {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 60%;
  height: 60%;
  border: 3px solid;
  border-image: linear-gradient(225deg, #ffd700, #ffed4e, #b8860b, #ffd700) 1;
  border-radius: 50%;
  animation: rotate 8s linear infinite reverse;
}

@keyframes rotate {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

.header h1 {
  color: #fff;
  font-size: 3rem;
  font-weight: 900;
  margin: 0;
  background: linear-gradient(135deg, #ffd700 0%, #ffed4e 50%, #ffd700 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  text-shadow: 0 0 30px rgba(255, 215, 0, 0.3);
  letter-spacing: -1px;
  position: relative;
}

.logout-btn {
  background: linear-gradient(135deg, #e74c3c 0%, #c0392b 100%);
  color: white;
  border: none;
  border-radius: 15px;
  padding: 12px 24px;
  cursor: pointer;
  font-size: 0.95rem;
  font-weight: 700;
  transition: all 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
  box-shadow: 0 4px 15px rgba(231, 76, 60, 0.4);
  display: flex;
  align-items: center;
  gap: 8px;
  position: relative;
  overflow: hidden;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.logout-btn::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 0;
  height: 0;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.2);
  transform: translate(-50%, -50%);
  transition: width 0.6s, height 0.6s;
}

.logout-btn:hover::before {
  width: 300px;
  height: 300px;
}

.logout-btn:hover {
  transform: translateY(-3px) scale(1.05);
  box-shadow: 0 8px 25px rgba(231, 76, 60, 0.6);
}

.logout-btn:active {
  transform: translateY(-1px) scale(1.02);
}

.logout-icon {
  width: 20px;
  height: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  z-index: 1;
}

.logout-icon svg {
  width: 100%;
  height: 100%;
}

.logout-text {
  position: relative;
  z-index: 1;
}

/* Karat Tabs */
.karat-tabs {
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 12px;
  margin-bottom: 50px;
  position: relative;
  z-index: 1;
}

.karat-tab {
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.05), rgba(255, 255, 255, 0.02));
  border: 2px solid rgba(255, 215, 0, 0.2);
  border-radius: 50px;
  padding: 14px 28px;
  cursor: pointer;
  font-size: 1.05rem;
  font-weight: 700;
  color: rgba(255, 255, 255, 0.6);
  transition: all 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
  position: relative;
  overflow: hidden;
  backdrop-filter: blur(10px);
}

.karat-shine {
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.2), transparent);
  transition: left 0.6s;
}

.karat-tab:hover .karat-shine {
  left: 100%;
}

.karat-tab:hover {
  transform: translateY(-3px) scale(1.05);
  border-color: rgba(255, 215, 0, 0.5);
  color: rgba(255, 255, 255, 0.9);
  box-shadow: 0 5px 20px rgba(255, 215, 0, 0.2);
}

.karat-tab.active {
  background: linear-gradient(135deg, #ffd700 0%, #ffed4e 100%);
  border-color: #ffd700;
  color: #1a1a2e;
  box-shadow: 
    0 5px 25px rgba(255, 215, 0, 0.5),
    inset 0 -2px 10px rgba(0, 0, 0, 0.1);
  transform: translateY(-3px) scale(1.08);
}

.karat-value {
  position: relative;
  z-index: 1;
}

/* Calculator Section */
.karat-calculator {
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.05), rgba(255, 255, 255, 0.02));
  border-radius: 25px;
  padding: 40px;
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 215, 0, 0.2);
  box-shadow: 
    0 20px 40px rgba(0, 0, 0, 0.3),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
  position: relative;
  z-index: 1;
  animation: slideInUp 0.6s cubic-bezier(0.34, 1.56, 0.64, 1);
}

@keyframes slideInUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.karat-header {
  text-align: center;
  margin-bottom: 40px;
  position: relative;
}

.header-content h2 {
  color: #fff;
  font-size: 2.5rem;
  margin: 0 0 20px 0;
  font-weight: 800;
  background: linear-gradient(135deg, #ffd700, #ffed4e);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  letter-spacing: -0.5px;
}

.purity-info {
  display: flex;
  justify-content: center;
}

.purity-badge {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  background: linear-gradient(135deg, #ffd700, #ffed4e);
  color: #1a1a2e;
  padding: 10px 20px;
  border-radius: 50px;
  font-size: 1rem;
  font-weight: 700;
  box-shadow: 
    0 4px 15px rgba(255, 215, 0, 0.4),
    inset 0 -2px 8px rgba(0, 0, 0, 0.1);
  animation: badgePulse 2s ease-in-out infinite;
}

@keyframes badgePulse {
  0%, 100% { 
    box-shadow: 0 4px 15px rgba(255, 215, 0, 0.4), inset 0 -2px 8px rgba(0, 0, 0, 0.1);
  }
  50% { 
    box-shadow: 0 6px 25px rgba(255, 215, 0, 0.6), inset 0 -2px 8px rgba(0, 0, 0, 0.1);
  }
}

/* Input Section */
.calculation-section {
  margin-bottom: 40px;
}

.input-group {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 25px;
  margin-bottom: 35px;
}

.input-field {
  position: relative;
}

.input-field label {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 12px;
  font-weight: 700;
  color: rgba(255, 255, 255, 0.8);
  font-size: 0.95rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.label-icon {
  font-size: 1.2rem;
  filter: drop-shadow(0 2px 4px rgba(255, 215, 0, 0.3));
}

.input-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.input-wrapper input {
  width: 100%;
  padding: 16px 50px;
  border: 2px solid rgba(255, 215, 0, 0.2);
  border-radius: 15px;
  background: rgba(255, 255, 255, 0.05);
  color: #fff;
  font-size: 1.1rem;
  font-weight: 600;
  transition: all 0.3s ease;
  backdrop-filter: blur(10px);
}

.input-wrapper input:focus {
  outline: none;
  border-color: #ffd700;
  background: rgba(255, 255, 255, 0.08);
  box-shadow: 
    0 0 0 4px rgba(255, 215, 0, 0.1),
    0 5px 20px rgba(255, 215, 0, 0.2);
  transform: translateY(-2px);
}

.input-wrapper input::placeholder {
  color: rgba(255, 255, 255, 0.3);
}

.currency, .unit {
  position: absolute;
  font-weight: 800;
  font-size: 1.1rem;
  color: #ffd700;
  z-index: 2;
  text-shadow: 0 2px 8px rgba(255, 215, 0, 0.5);
}

.currency {
  left: 18px;
}

.unit {
  right: 18px;
  font-size: 0.9rem;
}

.input-underline {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 0;
  height: 3px;
  background: linear-gradient(90deg, #ffd700, #ffed4e);
  transition: width 0.3s ease;
  border-radius: 2px;
}

.input-field.focused .input-underline {
  width: 100%;
}

/* Error Messages */
.error-slide-enter-active, .error-slide-leave-active {
  transition: all 0.3s ease;
}

.error-slide-enter-from {
  opacity: 0;
  transform: translateY(-10px);
}

.error-slide-leave-to {
  opacity: 0;
  transform: translateY(-10px);
}

.error-message {
  display: flex;
  align-items: center;
  gap: 6px;
  color: #ff6b6b;
  font-size: 0.85rem;
  margin-top: 8px;
  font-weight: 600;
  animation: errorShake 0.5s ease;
}

@keyframes errorShake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-5px); }
  75% { transform: translateX(5px); }
}

.error-icon {
  font-size: 1rem;
}

.input-field.error input {
  border-color: #ff6b6b;
  box-shadow: 0 0 0 4px rgba(255, 107, 107, 0.1);
}

/* Calculate Button */
.calculate-btn {
  position: relative;
  width: 100%;
  padding: 20px 40px;
  border: none;
  border-radius: 18px;
  cursor: pointer;
  font-size: 1.3rem;
  font-weight: 800;
  overflow: hidden;
  transition: all 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
  text-transform: uppercase;
  letter-spacing: 1px;
}

.btn-background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #ffd700 0%, #ffed4e 50%, #ffd700 100%);
  background-size: 200% 200%;
  animation: gradientShift 3s ease infinite;
  z-index: 0;
}

@keyframes gradientShift {
  0%, 100% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
}

.btn-content {
  position: relative;
  z-index: 2;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  color: #1a1a2e;
}

.btn-icon {
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.btn-icon svg {
  width: 100%;
  height: 100%;
  stroke: #1a1a2e;
}

.btn-ripple {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 0;
  height: 0;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.5);
  transform: translate(-50%, -50%);
  transition: width 0.6s, height 0.6s;
  z-index: 1;
}

.calculate-btn:hover {
  transform: translateY(-4px) scale(1.02);
  box-shadow: 
    0 12px 35px rgba(255, 215, 0, 0.5),
    inset 0 -3px 15px rgba(0, 0, 0, 0.2);
}

.calculate-btn:hover .btn-ripple {
  width: 500px;
  height: 500px;
}

.calculate-btn:active {
  transform: translateY(-2px) scale(1.01);
}

/* Results Section */
.result-appear-enter-active, .result-appear-leave-active {
  transition: all 0.6s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.result-appear-enter-from {
  opacity: 0;
  transform: translateY(30px) scale(0.9);
}

.result-appear-leave-to {
  opacity: 0;
  transform: translateY(-30px) scale(0.9);
}

.result-display {
  background: linear-gradient(135deg, rgba(255, 215, 0, 0.1), rgba(255, 237, 78, 0.1));
  border-radius: 20px;
  padding: 35px;
  border: 2px solid rgba(255, 215, 0, 0.3);
  backdrop-filter: blur(20px);
  box-shadow: 
    0 15px 35px rgba(0, 0, 0, 0.3),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
}

.result-header {
  position: relative;
  text-align: center;
  margin-bottom: 30px;
  padding-bottom: 20px;
}

.result-header h3 {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  color: #fff;
  font-size: 2rem;
  margin: 0;
  font-weight: 800;
  background: linear-gradient(135deg, #ffd700, #ffed4e);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.header-icon {
  font-size: 2rem;
  filter: drop-shadow(0 2px 8px rgba(255, 215, 0, 0.5));
}

.result-decoration {
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 100px;
  height: 3px;
  background: linear-gradient(90deg, transparent, #ffd700, transparent);
  border-radius: 2px;
}

.calculation-breakdown {
  display: grid;
  gap: 16px;
}

.calc-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 24px;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 12px;
  border-left: 4px solid rgba(255, 215, 0, 0.5);
  transition: all 0.3s ease;
  backdrop-filter: blur(10px);
}

.calc-item:hover {
  transform: translateX(5px);
  background: rgba(255, 255, 255, 0.08);
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.2);
}

.calc-item .label {
  display: flex;
  align-items: center;
  gap: 10px;
  font-weight: 600;
  color: rgba(255, 255, 255, 0.8);
  font-size: 1rem;
}

.label-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: rgba(255, 215, 0, 0.5);
  box-shadow: 0 0 10px rgba(255, 215, 0, 0.3);
}

.label-dot.gold {
  background: #ffd700;
  box-shadow: 0 0 15px rgba(255, 215, 0, 0.6);
  animation: dotPulse 2s ease-in-out infinite;
}

@keyframes dotPulse {
  0%, 100% { 
    transform: scale(1);
    box-shadow: 0 0 15px rgba(255, 215, 0, 0.6);
  }
  50% { 
    transform: scale(1.3);
    box-shadow: 0 0 25px rgba(255, 215, 0, 0.8);
  }
}

.calc-item .value {
  font-weight: 700;
  color: #fff;
  font-size: 1.1rem;
  text-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
}

.calc-item.subtotal {
  background: linear-gradient(135deg, rgba(255, 183, 0, 0.1), rgba(255, 152, 0, 0.1));
  border-left-color: #ffb300;
}

.calc-item.subtotal .value {
  color: #ffb300;
  font-size: 1.2rem;
}

.calc-item.tax {
  background: linear-gradient(135deg, rgba(231, 76, 60, 0.1), rgba(192, 57, 43, 0.1));
  border-left-color: #e74c3c;
}

.calc-item.tax .value {
  color: #ff6b6b;
  font-size: 1.1rem;
}

.calc-item.total {
  background: linear-gradient(135deg, rgba(46, 213, 115, 0.15), rgba(34, 166, 90, 0.15));
  border-left-color: #2ecc71;
  border-left-width: 6px;
  padding: 20px 24px;
  margin-top: 10px;
  box-shadow: 0 8px 25px rgba(46, 213, 115, 0.2);
}

.calc-item.total .label {
  font-size: 1.1rem;
  color: #fff;
}

.total-amount {
  background: linear-gradient(135deg, #2ecc71, #27ae60);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  font-size: 1.5rem;
  font-weight: 900;
  animation: totalPulse 2s ease-in-out infinite;
}

@keyframes totalPulse {
  0%, 100% { 
    transform: scale(1);
    filter: drop-shadow(0 0 10px rgba(46, 213, 115, 0.3));
  }
  50% { 
    transform: scale(1.05);
    filter: drop-shadow(0 0 20px rgba(46, 213, 115, 0.5));
  }
}

/* Responsive Design */
@media (max-width: 768px) {
  .gold-calculator {
    padding: 30px 20px;
    border-radius: 20px;
  }

  .header {
    flex-direction: column;
    gap: 20px;
  }

  .logo-section {
    flex-direction: column;
    gap: 15px;
  }

  .logo-icon {
    width: 60px;
    height: 60px;
  }

  .header h1 {
    font-size: 2rem;
  }

  .logout-btn {
    padding: 10px 20px;
    font-size: 0.85rem;
  }

  .logout-text {
    display: none;
  }

  .karat-tabs {
    gap: 8px;
  }

  .karat-tab {
    padding: 12px 20px;
    font-size: 0.95rem;
  }

  .karat-calculator {
    padding: 25px 20px;
  }

  .header-content h2 {
    font-size: 1.8rem;
  }

  .input-group {
    grid-template-columns: 1fr;
    gap: 20px;
  }

  .calculate-btn {
    font-size: 1.1rem;
    padding: 18px 30px;
  }

  .result-display {
    padding: 25px 20px;
  }

  .result-header h3 {
    font-size: 1.6rem;
  }

  .calc-item {
    padding: 14px 18px;
    flex-direction: column;
    align-items: flex-start;
    gap: 8px;
  }

  .calc-item .value {
    align-self: flex-end;
  }
}
</style>