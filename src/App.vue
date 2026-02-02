<template>
  <div id="app">
    <UserRegistration v-if="currentScreen === 'registration'" @registration-success="handleRegistrationSuccess" />
    <TransitionScreen v-if="currentScreen === 'transition'" @transition-complete="handleTransitionComplete" />
    <GoldCalculator v-if="currentScreen === 'calculator'" @logout="handleLogout" />
  </div>
</template>

<script>
import UserRegistration from './Registration.vue'
import TransitionScreen from './components/TransitionScreen.vue'
import GoldCalculator from './components/GoldCalculator.vue'

export default {
  name: 'App',
  components: {
    UserRegistration,
    TransitionScreen,
    GoldCalculator
  },
  data() {
    return {
      currentScreen: 'registration'
    }
  },
  methods: {
    handleRegistrationSuccess() {
      this.currentScreen = 'transition'
    },
    handleTransitionComplete() {
      this.currentScreen = 'calculator'
    },
    handleLogout() {
      this.currentScreen = 'registration'
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background: linear-gradient(135deg, #1a1a1a 0%, #2d2d2d 100%);
  min-height: 100vh;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

#app {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  background: url('data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"><circle cx="50" cy="50" r="2" fill="%23ffd700" opacity="0.1"/></svg>') repeat;
}
</style>
