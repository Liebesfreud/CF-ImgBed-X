<template>
  <div class="whitelist-container">
    <!-- 动态背景图片 -->
    <div class="background-wrapper" v-html="backgroundImagesTemplate"></div>
    
    <div class="whitelist-content">
      <!-- 返回首页按钮 -->
      <div class="back-button-wrapper">
        <BaseButton
          class="back-button"
          @click="goHome"
          round
          size="lg"
          variant="secondary"
        >
          <font-awesome-icon icon="home" />
        </BaseButton>
      </div>

      <!-- 白名单图标和动画 -->
      <div class="status-animation">
        <div class="status-icon">
          <font-awesome-icon icon="shield-alt" class="shield-icon" />
          <div class="status-badge">
            <font-awesome-icon icon="clock" class="clock-icon" />
          </div>
        </div>
      </div>
      
      <!-- 提示信息 -->
      <div class="status-info">
        <h1 class="status-title">{{ $t('whitelist.title') }}</h1>
        <p class="status-description">
          {{ $t('whitelist.description') }}
        </p>
        
        <!-- 操作按钮 -->
        <div class="status-actions">
          <BaseButton
            variant="primary"
            size="lg"
            class="action-btn primary-btn"
            @click="goHome"
          >
            <font-awesome-icon icon="home" class="btn-icon" />
            {{ $t('whitelist.goHome') }}
          </BaseButton>

          <BaseButton
            size="lg"
            class="action-btn secondary-btn"
            @click="goBack"
            variant="secondary"
          >
            <font-awesome-icon icon="arrow-left" class="btn-icon" />
            {{ $t('whitelist.goBack') }}
          </BaseButton>
        </div>
        
        <!-- 帮助信息 -->
        <div class="help-info">
          <p class="help-text">{{ $t('whitelist.helpText') }}</p>
          <div class="quick-links">
            <a href="javascript:void(0)" @click="goHome" class="quick-link">
              <font-awesome-icon icon="cloud-upload-alt" />
              {{ $t('whitelist.uploadImage') }}
            </a>
            <a href="javascript:void(0)" @click="refreshPage" class="quick-link">
              <font-awesome-icon icon="redo" />
              {{ $t('whitelist.refreshPage') }}
            </a>
          </div>
        </div>
        
        <!-- 项目信息 -->
        <div class="powered-by">
          <p>Powered By: 
            <a href="https://github.com/MarSeventh/CloudFlare-ImgBed" class="project-link">
              CloudFlare-ImgBed
            </a>
          </p>
        </div>
      </div>
    </div>
    
    <!-- 装饰性元素 -->
    <div class="floating-elements">
      <div class="floating-shape shape-1"></div>
      <div class="floating-shape shape-2"></div>
      <div class="floating-shape shape-3"></div>
      <div class="floating-shape shape-4"></div>
      <div class="floating-shape shape-5"></div>
    </div>
  </div>
</template>

<script>
import { useHead } from '@vueuse/head';
import { mapGetters } from 'vuex'
import backgroundManager from '@/mixins/backgroundManager'

export default {
    name: 'WhiteListOn',
    mixins: [backgroundManager],
    computed: {
        ...mapGetters(['useDarkMode', 'userConfig']),
    },
    setup() {
        useHead({
            title: 'White List On',
            meta: [
                { name: 'robots', content: 'noindex, nofollow' },
                { name: 'viewport', content: 'width=device-width, initial-scale=1' },
                { charset: 'UTF-8' }
            ],
        });
    },
    mounted() {
        // 初始化背景图
        this.initializeBackground('uploadBkImg', '.whitelist-container', false, true)
    },
    beforeUnmount() {
        // 清理背景轮播定时器
        this.clearBackgroundInterval()
    },
    methods: {
        goHome() {
            this.$router.push('/')
        },
        goBack() {
            if (window.history.length > 1) {
                this.$router.go(-1)
            } else {
                this.$router.push('/')
            }
        },
        refreshPage() {
            window.location.reload()
        }
    }
}
</script>

<style scoped>
.whitelist-container {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow: hidden;
  background: var(--color-bg);
  color: var(--color-text);
}

.background-wrapper {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
}

.whitelist-content {
  text-align: center;
  z-index: 10;
  max-width: 600px;
  padding: var(--space-xl);
  position: relative;
  background: var(--color-surface);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-as-border-strong);
}

/* 返回按钮 */
.back-button-wrapper {
  position: absolute;
  top: -25px;
  right: -25px;
  z-index: 15;
}

.back-button {
  box-shadow: var(--shadow-as-border-strong);
  transition: background-color 0.3s cubic-bezier(0.4, 0, 0.2, 1), box-shadow 0.3s cubic-bezier(0.4, 0, 0.2, 1), transform 0.3s cubic-bezier(0.4, 0, 0.2, 1), color 0.3s cubic-bezier(0.4, 0, 0.2, 1), border-color 0.3s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.back-button:hover {
  transform: translateY(-2px) scale(1.1);
  box-shadow: var(--shadow-as-border-strong);
}

/* 状态动画部分 */
.status-animation {
  margin-bottom: var(--space-xl);
  position: relative;
}

.status-icon {
  position: relative;
  display: inline-block;
  margin-bottom: var(--space-md);
}

.shield-icon {
  font-size: 4rem;
  color: var(--color-accent);
  animation: pulse 2s ease-in-out infinite;
  filter: drop-shadow(0 0 20px color-mix(in srgb, var(--color-accent) 25%, transparent));
}

.status-badge {
  position: absolute;
  bottom: -5px;
  right: -5px;
  background: var(--color-danger);
  border-radius: var(--radius-full);
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 3px solid var(--color-surface);
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}

.clock-icon {
  color: var(--color-accent-contrast);
  font-size: 0.8rem;
  animation: tick 1s ease-in-out infinite;
}

/* 状态信息 */
.status-info {
  animation: fadeInUp 1s ease-out 0.5s both;
}

.status-title {
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: var(--space-md);
  color: var(--color-text);
  background: linear-gradient(45deg, var(--color-accent), var(--color-success));
  background-clip: text;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.status-description {
  font-size: 1.1rem;
  line-height: 1.6;
  margin-bottom: var(--space-md);
  opacity: 0.9;
  color: var(--color-text);
}

.status-description-en {
  font-size: 0.95rem;
  line-height: 1.5;
  margin-bottom: var(--space-xl);
  opacity: 0.7;
  color: var(--color-text-muted);
  font-style: italic;
}

/* 操作按钮 */
.status-actions {
  display: flex;
  gap: var(--space-md);
  justify-content: center;
  flex-wrap: wrap;
  margin-bottom: var(--space-xl);
}

.action-btn {
  font-weight: 600;
  transition: background-color 0.3s cubic-bezier(0.4, 0, 0.2, 1), box-shadow 0.3s cubic-bezier(0.4, 0, 0.2, 1), transform 0.3s cubic-bezier(0.4, 0, 0.2, 1), color 0.3s cubic-bezier(0.4, 0, 0.2, 1), border-color 0.3s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  min-width: 140px;
}

.action-btn:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-as-border-strong);
}

.btn-icon {
  margin-right: var(--space-sm);
}

/* 帮助信息 */
.help-info {
  animation: fadeInUp 1s ease-out 1s both;
  margin-bottom: var(--space-lg);
}

.help-text {
  margin-bottom: var(--space-md);
  opacity: 0.7;
  font-size: 0.9rem;
  color: var(--color-text-muted);
}

.quick-links {
  display: flex;
  gap: var(--space-md);
  justify-content: center;
  flex-wrap: wrap;
}

.quick-link {
  color: var(--color-accent);
  text-decoration: none;
  font-weight: 500;
  padding: var(--space-sm) var(--space-md);
  border-radius: var(--radius-sm);
  background: var(--color-surface-soft);
  transition: background-color 0.3s cubic-bezier(0.4, 0, 0.2, 1), box-shadow 0.3s cubic-bezier(0.4, 0, 0.2, 1), transform 0.3s cubic-bezier(0.4, 0, 0.2, 1), color 0.3s cubic-bezier(0.4, 0, 0.2, 1), border-color 0.3s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  box-shadow: var(--shadow-as-border);
  font-size: 0.85rem;
  display: flex;
  align-items: center;
  gap: var(--space-sm);
}

.quick-link:hover {
  background: var(--color-accent);
  color: var(--color-accent-contrast);
  transform: translateY(-2px);
  box-shadow: var(--shadow-as-border-strong);
}

/* 项目信息 */
.powered-by {
  animation: fadeInUp 1s ease-out 1.2s both;
  padding-top: var(--space-md);
  border-top: 1px solid var(--color-border);
  opacity: 0.8;
}

.powered-by p {
  margin: 0;
  font-size: 0.85rem;
  color: var(--color-text-muted);
}

.project-link {
  color: var(--color-accent);
  text-decoration: none;
  font-weight: 600;
  transition: background-color 0.3s ease, box-shadow 0.3s ease, transform 0.3s ease, color 0.3s ease, border-color 0.3s ease, opacity 0.3s ease;
}

.project-link:hover {
  color: var(--color-success);
  text-shadow: 0 0 10px rgba(103, 194, 58, 0.3);
}

/* 装饰性元素 */
.floating-elements {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 1;
}

.floating-shape {
  position: absolute;
  background: color-mix(in srgb, var(--color-accent) 10%, transparent);
  border-radius: var(--radius-full);
  animation: floatShapes 8s ease-in-out infinite;
}

.shape-1 {
  width: 60px;
  height: 60px;
  top: 15%;
  left: 10%;
  animation-delay: 0s;
}

.shape-2 {
  width: 80px;
  height: 80px;
  top: 60%;
  right: 15%;
  animation-delay: -2s;
  background: rgba(103, 194, 58, 0.1);
}

.shape-3 {
  width: 40px;
  height: 40px;
  bottom: 25%;
  left: 20%;
  animation-delay: -4s;
}

.shape-4 {
  width: 70px;
  height: 70px;
  top: 25%;
  right: 25%;
  animation-delay: -1s;
  background: rgba(245, 108, 108, 0.1);
}

.shape-5 {
  width: 50px;
  height: 50px;
  bottom: 15%;
  right: 10%;
  animation-delay: -3s;
  background: rgba(230, 162, 60, 0.1);
}

/* 动画定义 */
@keyframes pulse {
  0%, 100% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.05);
    opacity: 0.8;
  }
}

@keyframes tick {
  0%, 100% {
    transform: rotate(0deg);
  }
  50% {
    transform: rotate(180deg);
  }
}

@keyframes floatShapes {
  0%, 100% {
    transform: translateY(0) rotate(0deg);
    opacity: 0.6;
  }
  50% {
    transform: translateY(-25px) rotate(180deg);
    opacity: 0.3;
  }
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .whitelist-content {
    padding: var(--space-lg);
    margin: var(--space-md);
    max-width: calc(100% - var(--space-xl));
  }
  
  .back-button-wrapper {
    top: -20px;
    right: -20px;
  }
  
  .shield-icon {
    font-size: 3rem;
  }
  
  .status-title {
    font-size: 1.5rem;
  }
  
  .status-description {
    font-size: 1rem;
  }
  
  .status-description-en {
    font-size: 0.9rem;
  }
  
  .status-actions {
    flex-direction: column;
    align-items: center;
  }
  
  .action-btn {
    width: 200px;
  }
  
  .quick-links {
    flex-direction: column;
    gap: var(--space-sm);
    align-items: center;
  }
  
  .quick-link {
    width: fit-content;
    min-width: 120px;
    justify-content: center;
  }
}

@media (max-width: 480px) {
  .whitelist-content {
    padding: var(--space-md);
    margin: var(--space-sm);
    max-width: calc(100% - var(--space-md));
  }
  
  .status-title {
    font-size: 1.3rem;
  }
  
  .status-description {
    font-size: 0.95rem;
  }
  
  .status-description-en {
    font-size: 0.85rem;
  }
  
  .shield-icon {
    font-size: 2.5rem;
  }
}
</style>  