<template>
    <div class="dashboard-shell-nav">
        <div class="brand-mark" aria-label="CF ImgBed X">CF ImgBed X</div>

        <nav class="primary-nav" aria-label="Primary">
            <BaseButton
                v-for="item in navItems"
                :key="item.key"
                type="button"
                variant="ghost"
                class="nav-item"
                :class="{ 'is-active': activeTab === item.key }"
                @click="handleTabClick(item.route)"
            >
                <component :is="item.icon" :size="19" :stroke-width="1.8" />
                <span>{{ item.label }}</span>
            </BaseButton>
        </nav>

        <div class="nav-controls">
            <AdminToggleDark />
            <slot name="actions" />
        </div>
    </div>
</template>

<script>
import AdminToggleDark from './dashboard/AdminToggleDark.vue';
import { IconCameraUp, IconPhoto, IconSettings, IconUserCog } from '@tabler/icons-vue';

export default {
    name: 'DashboardTabs',
    props: {
        activeTab: {
            type: String,
            default: 'home'
        }
    },
    components: {
        AdminToggleDark,
        IconCameraUp
    },
    computed: {
        navItems() {
            return [
                { key: 'home', route: '/', icon: IconCameraUp, label: '主页' },
                { key: 'gallery', route: '/dashboard', icon: IconPhoto, label: '图库' },
                { key: 'users', route: '/customerConfig', icon: IconUserCog, label: '用户' },
                { key: 'settings', route: '/systemConfig', icon: IconSettings, label: '设置' }
            ];
        }
    },
    methods: {
        handleTabClick(route) {
            if (this.$route.path === route) return;
            this.$router.push(route);
        }
    }
}
</script>

<style scoped>
.dashboard-shell-nav {
    display: grid;
    grid-template-columns: minmax(180px, 1fr) auto minmax(180px, 1fr);
    align-items: center;
    gap: 14px;
    width: 100%;
    min-height: 46px;
}

.brand-mark {
    justify-self: start;
    display: inline-flex;
    align-items: center;
    min-height: 46px;
    color: var(--color-text);
    font-size: 16px;
    font-weight: 800;
    letter-spacing: -0.02em;
    white-space: nowrap;
}

.primary-nav {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 12px;
    min-width: 0;
    margin: -4px 0;
    padding: 4px 0;
    overflow-x: auto;
    overflow-y: visible;
    scrollbar-width: none;
}

.primary-nav::-webkit-scrollbar {
    display: none;
}

.nav-item {
    flex: 0 0 auto;
    min-width: 88px;
    min-height: 38px;
    padding: 0 16px;
    overflow: visible;
    font-size: 14px;
    font-weight: 650;
    line-height: 1;
    border-radius: var(--radius-sm);
    color: var(--color-text-muted);
}

.nav-item :deep(.base-button__icon),
.nav-item :deep(.base-button__label) {
    display: inline-flex;
    align-items: center;
    line-height: 1;
}

.nav-item :deep(svg) {
    display: block;
}

.nav-item.is-active {
    --base-button-shadow: var(--shadow-as-border-strong);

    color: var(--color-accent-contrast);
    background: var(--color-accent);
}

.nav-controls {
    justify-self: end;
    display: inline-flex;
    align-items: center;
    justify-content: flex-end;
    gap: 8px;
    min-width: max-content;
    min-height: 46px;
}


@media (max-width: 860px) {
    .dashboard-shell-nav {
        grid-template-columns: 1fr;
        gap: 6px;
    }

    .brand-mark {
        justify-self: center;
        min-height: 30px;
        font-size: 14px;
    }

    .primary-nav { justify-content: center; }
    .nav-controls {
        justify-self: center;
        justify-content: center;
    }
}

@media (max-width: 560px) {
    .primary-nav {
        width: 100%;
        gap: 8px;
    }

    .nav-item {
        flex: 1 0 0;
        min-width: 0;
        padding: 0 10px;
        font-size: 12px;
    }
}
</style>
