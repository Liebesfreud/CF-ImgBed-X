<template>
    <div class="container">
        <header class="app-topbar">
            <div class="header-content">
                <DashboardTabs activeTab="settings">
                    <template #actions>
                        <el-tooltip :disabled="disableTooltip" :content="$t('sysConfig.logout')" placement="bottom">
                            <BaseButton class="nav-action danger" icon="sign-out-alt" variant="ghost" size="md" aria-label="Logout" @click="handleLogout" />
                        </el-tooltip>
                    </template>
                </DashboardTabs>
            </div>
        </header>
        <main class="settings-layout">
            <section class="settings-section-nav">
                <SysConfigTabs
                    v-model:activeIndex="activeIndex"
                    v-model:isCollapse="isSidebarCollapse"
                    class="settings-subnav"
                />
            </section>
            <!-- 根据锚点动态渲染子页面 -->
            <component :is="currentComponent" class="main-container" />
        </main>
    </div>
</template>
<script>
import DashboardTabs from '@/components/DashboardTabs.vue';
import SysConfigTabs from '@/components/config/SysConfigTabs.vue';
import SysCogStatus from '@/components/config/SysCogStatus.vue';
import SysCogUpload from '@/components/config/SysCogUpload.vue';
import SysCogSecurity from '@/components/config/SysCogSecurity.vue';
import SysCogPage from '@/components/config/SysCogPage.vue';
import SysCogOthers from '@/components/config/SysCogOthers.vue';
import backgroundManager from '@/mixins/backgroundManager';

export default {
    name: 'SystemConfig',
    mixins: [backgroundManager],
    data() {
        return {
            activeIndex: 'status',
            isSidebarCollapse: false
        }
    },
    watch: {
        // 监听锚点变化
        '$route.hash': {
            immediate: true,
            handler(newHash) {
                this.activeIndex = newHash.replace('#', '');
                window.scrollTo(0, 0); // 滚动到页面顶部
            }
        },
        activeIndex(newIndex) {
            // 更新锚点
            const hash = `#${newIndex}`;
            this.$router.push({ hash });
        }
    },
    components: {
        DashboardTabs,
        SysConfigTabs,
        SysCogStatus,
        SysCogUpload,
        SysCogSecurity,
        SysCogPage,
        SysCogOthers
    },
    computed: {
        disableTooltip() {
            return window.innerWidth < 768;
        },
        // 根据锚点动态返回对应的组件
        currentComponent() {
            const hash = this.$route.hash.replace('#', '');
            switch (hash) {
                case 'status':
                    return SysCogStatus;
                case 'upload':
                    return SysCogUpload;
                case 'security':
                    return SysCogSecurity;
                case 'page':
                    return SysCogPage;
                case 'others':
                    return SysCogOthers;
                default:
                    return SysCogStatus;
            }
        }
    },
    methods: {
        handleLogout() {
            const url = process.env.NODE_ENV === 'production' ? '/api/auth/logout' : '/api/api/auth/logout';
            fetch(url, {
                method: 'POST',
                credentials: 'include',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ authType: 'admin' })
            }).finally(() => {
                this.$store.commit('setAdminLoggedIn', false);
                this.$router.push('/adminLogin');
            });
        },
        // 设置默认锚点
        setDefaultHash() {
            const defaultHash = '#status'; // 默认锚点
            window.location.hash = defaultHash;
            this.activeIndex = defaultHash.replace('#', '');
        },
    },
    mounted() {
        // 初始化背景图
        this.initializeBackground('adminBkImg', '.container', false, true);

        // 如果 URL 中没有锚点，则设置默认锚点
        if (!window.location.hash) {
            this.setDefaultHash();
        }
    },
}
</script>
<style scoped>
.container {
    background: transparent;
    min-height: 100vh;
}


.app-topbar {
    position: sticky;
    top: 12px;
    z-index: 200;
    width: min(1080px, calc(100% - 32px));
    margin: 10px auto 16px;
    padding: 8px 12px;
    border-radius: var(--radius-xl);
    background: var(--color-surface);
    box-shadow: var(--shadow-as-border);
}

.header-content {
    display: flex;
    justify-content: center;
    align-items: center;
}

.icon-action {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: var(--control-height-md);
    height: var(--control-height-md);
    border: none;
    border-radius: 999px;
    background: var(--color-surface);
    color: var(--color-text);
    cursor: pointer;
    transition: transform var(--motion-fast) var(--motion-ease), box-shadow var(--motion-fast) var(--motion-ease);
}

.icon-action:hover {
    transform: translateY(-1px);
    box-shadow: var(--shadow-as-border-strong);
}

.header-icon {
    color: currentColor;
}

.settings-layout {
    width: min(1120px, calc(100% - 32px));
    margin: 22px auto 0;
    padding-bottom: 48px;
}

.settings-section-nav {
    display: flex;
    align-items: center;
    gap: var(--space-2);
    margin-bottom: var(--space-5);
    padding: var(--space-2);
    border-radius: var(--radius-xl);
    background: var(--color-surface-soft);
    box-shadow: var(--shadow-as-border);
    overflow-x: auto;
}


.settings-subnav {
    flex: 1 1 auto;
}

.main-container {
    width: 100%;
}

@media (max-width: 768px) {
    .app-topbar {
        top: 6px;
        width: calc(100% - 16px);
        border-radius: 18px;
        padding: 8px;
    }

    .settings-layout {
        width: calc(100% - 20px);
        margin-top: 18px;
    }
}
</style>