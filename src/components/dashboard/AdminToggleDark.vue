<template>
    <div
      id="themeToggle"
      @click="handleToggleClick"
    >
      <transition name="icon-fade" mode="out-in">
        <font-awesome-icon
          :key="themeIcon"
          :icon="themeIcon"
          class="theme-icon"
        />
      </transition>
    </div>
</template>
  
<script>
export default {
  name: 'ToggleDark',
  data() {
    return {
      isDark: this.$store.getters.useDarkMode,
      isAuto: !this.$store.getters.cusDarkMode,
    };
  },
  computed: {
    themeIcon() {
      if (this.isAuto) return 'adjust';
      return this.isDark ? 'moon' : 'sun';
    }
  },
  methods: {
    handleToggleClick() {
      // 三种状态循环：亮色 -> 暗色 -> 跟随系统 -> 亮色
      if (this.isAuto) {
        // 当前是自动模式，切换到亮色
        this.isDark = false;
        this.isAuto = false;
        this.$store.commit('setCusDarkMode', true);
        this.$store.commit('setUseDarkMode', false);
      } else if (!this.isDark) {
        // 当前是亮色，切换到暗色
        this.isDark = true;
        this.$store.commit('setCusDarkMode', true);
        this.$store.commit('setUseDarkMode', true);
      } else {
        // 当前是暗色，切换到跟随系统
        this.isAuto = true;
        this.$store.commit('setCusDarkMode', false);
      }
    }
  }
};
</script>

<style scoped>
#themeToggle {
  border: 0;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 2rem;
  height: 2rem;
  border-radius: 0.375rem;
  transition-property: background-color, border-color, color;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
  transition-duration: 150ms;
}

.theme-icon {
  font-size: 1.3em;
  color: var(--admin-theme-toggle-color);
}

.icon-fade-enter-active,
.icon-fade-leave-active {
  transition: opacity 0.3s ease-in-out, transform 0.3s ease-in-out;
}

.icon-fade-enter-from {
  opacity: 0;
  transform: scale(0.8) rotate(-90deg);
}

.icon-fade-leave-to {
  opacity: 0;
  transform: scale(0.8) rotate(90deg);
}

.icon-fade-enter-to,
.icon-fade-leave-from {
  opacity: 1;
  transform: scale(1) rotate(0deg);
}

</style>