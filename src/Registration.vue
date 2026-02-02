<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div class="registration-container">
    <!-- Animated Background -->
    <div class="background-effects">
      <div class="bg-gradient"></div>
      <div class="floating-shapes">
        <div class="shape shape-1"></div>
        <div class="shape shape-2"></div>
        <div class="shape shape-3"></div>
      </div>
      <div class="grid-overlay"></div>
    </div>

    <!-- Registration Form -->
    <div class="registration-form">
      <!-- Logo Section -->
      <div class="logo-container">
        <div class="logo">
          <div class="logo-ring">
            <div class="logo-shine"></div>
          </div>
          <div class="logo-center">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="M12 2L2 7l10 5 10-5-10-5z"></path>
              <path d="M2 17l10 5 10-5"></path>
              <path d="M2 12l10 5 10-5"></path>
            </svg>
          </div>
        </div>
      </div>

      <!-- Header -->
      <div class="form-header">
        <h1>Welcome to Prima Gold</h1>
        <p>Create your account to access premium gold calculations</p>
      </div>

      <!-- Form -->
      <form @submit.prevent="register">
        <div class="form-group" :class="{ focused: focusedField === 'name', filled: form.name }">
          <label for="name">
            <span class="label-icon">
              <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M20 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2"></path>
                <circle cx="12" cy="7" r="4"></circle>
              </svg>
            </span>
            <span class="label-text">Full Name</span>
          </label>
          <div class="input-wrapper">
            <input
              type="text"
              id="name"
              v-model="form.name"
              placeholder="Enter your full name"
              @focus="focusedField = 'name'"
              @blur="focusedField = null"
              required
            >
            <div class="input-border"></div>
          </div>
        </div>

        <div class="form-group" :class="{ focused: focusedField === 'email', filled: form.email }">
          <label for="email">
            <span class="label-icon">
              <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"></path>
                <polyline points="22,6 12,13 2,6"></polyline>
              </svg>
            </span>
            <span class="label-text">Email Address</span>
          </label>
          <div class="input-wrapper">
            <input
              type="email"
              id="email"
              v-model="form.email"
              placeholder="Enter your email"
              @focus="focusedField = 'email'"
              @blur="focusedField = null"
              required
            >
            <div class="input-border"></div>
          </div>
        </div>

        <div class="form-group" :class="{ focused: focusedField === 'phone', filled: form.phone }">
          <label for="phone">
            <span class="label-icon">
              <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"></path>
              </svg>
            </span>
            <span class="label-text">Phone Number</span>
          </label>
          <div class="input-wrapper">
            <input
              type="tel"
              id="phone"
              v-model="form.phone"
              placeholder="Enter your phone number"
              @focus="focusedField = 'phone'"
              @blur="focusedField = null"
              required
            >
            <div class="input-border"></div>
          </div>
        </div>

        <button type="submit" class="register-btn" :disabled="!isFormValid" :class="{ active: isFormValid }">
          <span class="btn-background"></span>
          <span class="btn-content">
            <span class="btn-text">Create Account</span>
            <span class="btn-icon">
              <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                <line x1="5" y1="12" x2="19" y2="12"></line>
                <polyline points="12 5 19 12 12 19"></polyline>
              </svg>
            </span>
          </span>
          <div class="btn-ripple"></div>
        </button>
      </form>

      <!-- Footer -->
      <div class="form-footer">
        <p>By registering, you agree to our Terms & Conditions</p>
      </div>
    </div>

    <!-- Decorative Elements -->
    <div class="decorative-lines">
      <div class="line line-1"></div>
      <div class="line line-2"></div>
      <div class="line line-3"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'UserRegistration',
  data() {
    return {
      form: {
        name: '',
        email: '',
        phone: ''
      },
      focusedField: null
    }
  },
  computed: {
    isFormValid() {
      return this.form.name.trim() &&
             this.form.email.trim() &&
             this.form.phone.trim() &&
             this.isValidEmail(this.form.email)
    }
  },
  methods: {
    isValidEmail(email) {
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
      return emailRegex.test(email)
    },
    register() {
      if (this.isFormValid) {
        // Store user data
        localStorage.setItem('userData', JSON.stringify(this.form))

        // Add success animation
        const formElement = this.$el.querySelector('.registration-form')
        formElement.classList.add('success')

        // Emit success event after animation
        setTimeout(() => {
          this.$emit('registration-success')
        }, 600)
      }
    }
  }
}
</script>

<style scoped>
.registration-container {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #0f0f1e 0%, #1a1a2e 50%, #16213e 100%);
  padding: 40px 20px;
  position: relative;
  overflow: hidden;
}

/* Background Effects */
.background-effects {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 0;
}

.bg-gradient {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: radial-gradient(circle at 30% 50%, rgba(255, 215, 0, 0.1), transparent 50%),
              radial-gradient(circle at 70% 50%, rgba(255, 237, 78, 0.1), transparent 50%);
  animation: gradientPulse 10s ease-in-out infinite;
}

@keyframes gradientPulse {
  0%, 100% { opacity: 0.5; }
  50% { opacity: 1; }
}

.floating-shapes {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.shape {
  position: absolute;
  border-radius: 50%;
  background: radial-gradient(circle, rgba(255, 215, 0, 0.1), transparent);
  animation: floatShape 20s infinite ease-in-out;
}

.shape-1 {
  width: 300px;
  height: 300px;
  top: 10%;
  left: 10%;
  animation-duration: 25s;
}

.shape-2 {
  width: 200px;
  height: 200px;
  bottom: 20%;
  right: 15%;
  animation-duration: 30s;
  animation-delay: 5s;
}

.shape-3 {
  width: 150px;
  height: 150px;
  top: 60%;
  left: 70%;
  animation-duration: 35s;
  animation-delay: 10s;
}

@keyframes floatShape {
  0%, 100% { transform: translate(0, 0) scale(1); }
  25% { transform: translate(20px, -20px) scale(1.1); }
  50% { transform: translate(-15px, 15px) scale(0.9); }
  75% { transform: translate(15px, 20px) scale(1.05); }
}

.grid-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: 
    linear-gradient(rgba(255, 215, 0, 0.03) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255, 215, 0, 0.03) 1px, transparent 1px);
  background-size: 50px 50px;
  animation: gridMove 20s linear infinite;
}

@keyframes gridMove {
  0% { transform: translate(0, 0); }
  100% { transform: translate(50px, 50px); }
}

/* Registration Form */
.registration-form {
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.05), rgba(255, 255, 255, 0.02));
  border-radius: 30px;
  padding: 50px 45px;
  box-shadow: 
    0 30px 60px rgba(0, 0, 0, 0.5),
    0 0 0 1px rgba(255, 215, 0, 0.2),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
  max-width: 500px;
  width: 100%;
  backdrop-filter: blur(20px);
  position: relative;
  z-index: 1;
  animation: formSlideIn 0.8s cubic-bezier(0.34, 1.56, 0.64, 1);
  transition: all 0.6s cubic-bezier(0.34, 1.56, 0.64, 1);
}

@keyframes formSlideIn {
  from {
    opacity: 0;
    transform: scale(0.9) translateY(50px);
  }
  to {
    opacity: 1;
    transform: scale(1) translateY(0);
  }
}

.registration-form.success {
  animation: successPulse 0.6s cubic-bezier(0.34, 1.56, 0.64, 1);
}

@keyframes successPulse {
  0%, 100% { transform: scale(1); }
  50% { 
    transform: scale(1.05);
    box-shadow: 
      0 35px 70px rgba(46, 213, 115, 0.4),
      0 0 0 1px rgba(46, 213, 115, 0.5),
      inset 0 1px 0 rgba(255, 255, 255, 0.2);
  }
}

/* Logo Container */
.logo-container {
  display: flex;
  justify-content: center;
  margin-bottom: 30px;
  animation: logoAppear 1s cubic-bezier(0.34, 1.56, 0.64, 1) 0.3s both;
}

@keyframes logoAppear {
  from {
    opacity: 0;
    transform: scale(0) rotate(-180deg);
  }
  to {
    opacity: 1;
    transform: scale(1) rotate(0);
  }
}

.logo {
  position: relative;
  width: 80px;
  height: 80px;
}

.logo-ring {
  width: 100%;
  height: 100%;
  border: 3px solid;
  border-image: linear-gradient(45deg, #ffd700, #ffed4e, #ffd700) 1;
  border-radius: 50%;
  animation: logoRotate 10s linear infinite;
  position: relative;
  box-shadow: 
    0 0 30px rgba(255, 215, 0, 0.4),
    inset 0 0 30px rgba(255, 215, 0, 0.1);
}

@keyframes logoRotate {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

.logo-shine {
  position: absolute;
  top: -3px;
  left: -3px;
  right: -3px;
  bottom: -3px;
  border-radius: 50%;
  background: linear-gradient(135deg, transparent 40%, rgba(255, 255, 255, 0.3) 50%, transparent 60%);
  animation: shineRotate 3s linear infinite;
}

@keyframes shineRotate {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}

.logo-center {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 40px;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.logo-center svg {
  width: 100%;
  height: 100%;
  stroke: #ffd700;
  filter: drop-shadow(0 0 10px rgba(255, 215, 0, 0.5));
  animation: logoFloat 3s ease-in-out infinite;
}

@keyframes logoFloat {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-5px); }
}

/* Form Header */
.form-header {
  text-align: center;
  margin-bottom: 40px;
  animation: headerAppear 1s ease 0.5s both;
}

@keyframes headerAppear {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.form-header h1 {
  color: #fff;
  font-size: 2.5rem;
  margin: 0 0 10px 0;
  font-weight: 900;
  background: linear-gradient(135deg, #ffd700, #ffed4e);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  letter-spacing: -0.5px;
}

.form-header p {
  color: rgba(255, 255, 255, 0.7);
  font-size: 1rem;
  margin: 0;
  font-weight: 500;
}

/* Form Groups */
.form-group {
  margin-bottom: 30px;
  animation: inputAppear 0.6s ease both;
}

.form-group:nth-child(1) { animation-delay: 0.7s; }
.form-group:nth-child(2) { animation-delay: 0.8s; }
.form-group:nth-child(3) { animation-delay: 0.9s; }

@keyframes inputAppear {
  from {
    opacity: 0;
    transform: translateX(-30px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

.form-group label {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 12px;
  font-weight: 700;
  color: rgba(255, 255, 255, 0.8);
  font-size: 0.95rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  transition: all 0.3s ease;
}

.form-group.focused label,
.form-group.filled label {
  color: #ffd700;
}

.label-icon {
  width: 20px;
  height: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.label-icon svg {
  width: 100%;
  height: 100%;
  stroke: rgba(255, 255, 255, 0.6);
  transition: all 0.3s ease;
}

.form-group.focused .label-icon svg,
.form-group.filled .label-icon svg {
  stroke: #ffd700;
  filter: drop-shadow(0 0 8px rgba(255, 215, 0, 0.5));
}

.input-wrapper {
  position: relative;
}

.input-wrapper input {
  width: 100%;
  padding: 16px 20px;
  border: 2px solid rgba(255, 215, 0, 0.2);
  border-radius: 15px;
  background: rgba(255, 255, 255, 0.05);
  color: #fff;
  font-size: 1rem;
  font-weight: 500;
  transition: all 0.3s ease;
  backdrop-filter: blur(10px);
  box-sizing: border-box;
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
  font-style: italic;
}

.input-border {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 0;
  height: 3px;
  background: linear-gradient(90deg, #ffd700, #ffed4e);
  transition: width 0.3s ease;
  border-radius: 2px;
}

.form-group.focused .input-border {
  width: 100%;
}

/* Register Button */
.register-btn {
  position: relative;
  width: 100%;
  padding: 18px 40px;
  border: none;
  border-radius: 18px;
  cursor: pointer;
  font-size: 1.2rem;
  font-weight: 800;
  overflow: hidden;
  transition: all 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
  text-transform: uppercase;
  letter-spacing: 1px;
  margin-top: 10px;
  animation: btnAppear 0.6s ease 1s both;
}

@keyframes btnAppear {
  from {
    opacity: 0;
    transform: translateY(20px) scale(0.9);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.btn-background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, rgba(255, 215, 0, 0.3), rgba(255, 237, 78, 0.3));
  z-index: 0;
  transition: all 0.4s ease;
}

.register-btn.active .btn-background {
  background: linear-gradient(135deg, #ffd700 0%, #ffed4e 100%);
  background-size: 200% 200%;
  animation: gradientMove 3s ease infinite;
}

@keyframes gradientMove {
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
  color: rgba(255, 255, 255, 0.5);
  transition: all 0.3s ease;
}

.register-btn.active .btn-content {
  color: #1a1a2e;
}

.btn-icon {
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.btn-icon svg {
  width: 100%;
  height: 100%;
}

.btn-ripple {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 0;
  height: 0;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.3);
  transform: translate(-50%, -50%);
  transition: width 0.6s, height 0.6s;
  z-index: 1;
}

.register-btn:not(:disabled):hover {
  transform: translateY(-4px) scale(1.02);
  box-shadow: 0 12px 35px rgba(255, 215, 0, 0.5);
}

.register-btn:not(:disabled):hover .btn-ripple {
  width: 500px;
  height: 500px;
}

.register-btn:not(:disabled):active {
  transform: translateY(-2px) scale(1.01);
}

.register-btn:disabled {
  cursor: not-allowed;
  opacity: 0.5;
}

/* Form Footer */
.form-footer {
  text-align: center;
  margin-top: 30px;
  animation: footerAppear 0.6s ease 1.2s both;
}

@keyframes footerAppear {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

.form-footer p {
  color: rgba(255, 255, 255, 0.5);
  font-size: 0.85rem;
  margin: 0;
}

/* Decorative Lines */
.decorative-lines {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 0;
}

.line {
  position: absolute;
  background: linear-gradient(90deg, transparent, rgba(255, 215, 0, 0.2), transparent);
  animation: lineMove 15s linear infinite;
}

.line-1 {
  top: 20%;
  left: 0;
  width: 100%;
  height: 2px;
  animation-duration: 20s;
}

.line-2 {
  top: 50%;
  left: 0;
  width: 100%;
  height: 1px;
  animation-duration: 25s;
  animation-delay: 5s;
}

.line-3 {
  top: 80%;
  left: 0;
  width: 100%;
  height: 2px;
  animation-duration: 30s;
  animation-delay: 10s;
}

@keyframes lineMove {
  0% { transform: translateX(-100%); }
  100% { transform: translateX(100%); }
}

/* Responsive Design */
@media (max-width: 768px) {
  .registration-container {
    padding: 20px 15px;
  }

  .registration-form {
    padding: 40px 30px;
  }

  .logo {
    width: 70px;
    height: 70px;
  }

  .logo-center {
    width: 35px;
    height: 35px;
  }

  .form-header h1 {
    font-size: 2rem;
  }

  .form-header p {
    font-size: 0.9rem;
  }

  .form-group {
    margin-bottom: 25px;
  }

  .register-btn {
    font-size: 1.1rem;
    padding: 16px 35px;
  }
}
</style>