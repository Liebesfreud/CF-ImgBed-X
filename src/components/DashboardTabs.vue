<template>
    <div class="dashboard-shell-nav">
        <BaseButton class="brand-pill" variant="secondary" type="button" @click="goHome" aria-label="ImgHub">
            <IconCloudUpload class="brand-icon" :size="22" :stroke-width="1.9" />
            <span class="brand-copy">
                <span class="eyebrow">ImgHub</span>
                <strong>{{ activeMeta.label }}</strong>
            </span>
        </BaseButton>

        <nav class="primary-nav" aria-label="Primary">
            <BaseButton
                v-for="item in navItems"
                :key="item.key"
                type="button"
                :variant="activeTab === item.key ? 'secondary' : 'ghost'"
                class="nav-item"
                :class="{ 'is-active': activeTab === item.key }"
                @click="handleTabClick(item.route)"
            >
                <component :is="item.icon" :size="18" :stroke-width="1.8" />
                <span>{{ item.label }}</span>
            </BaseButton>
        </nav>

        <div class="nav-controls">
            <slot name="actions" />
            <AdminToggleDark />
            <LanguageSwitcher class="tabs-language-switcher" />
        </div>
    </div>
</template>

<script>
import AdminToggleDark from './dashboard/AdminToggleDark.vue';
import LanguageSwitcher from '@/components/LanguageSwitcher.vue';
import { IconCloudUpload, IconPhoto, IconSettings } from '@tabler/icons-vue';

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
        LanguageSwitcher,
        IconCloudUpload
    },
    computed: {
        navItems() {
            return [
                { key: 'home', route: '/', icon: IconCloudUpload, label: '主页' },
                { key: 'gallery', route: '/dashboard', icon: IconPhoto, label: '图库' },
                { key: 'settings', route: '/systemConfig', icon: IconSettings, label: '设置' }
            ];
        },
        activeMeta() {
            return this.navItems.find(item => item.key === this.activeTab) || this.navItems[0];
        }
    },
    methods: {
        goHome() {
            this.handleTabClick('/');
        },
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
    grid-template-columns: auto minmax(0, 1fr) auto;
    align-items: center;
    gap: 14px;
    width: 100%;
}

.brand-pill {
    gap: 10px;
    min-width: 0;
    background: transparent;
    box-shadow: none;
    padding: 7px 12px 7px 8px;
    border-radius: var(--radius-sm);
}

.brand-pill:hover {
    background: var(--color-surface-soft);
    box-shadow: none;
}

.brand-icon {
    color: var(--color-accent-contrast);
    background: var(--color-accent);
    border-radius: 50%;
    padding: 6px;
}

.brand-copy {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    line-height: 1.05;
    white-space: nowrap;
}

.eyebrow {
    color: var(--color-text-muted);
    font-size: 11px;
    letter-spacing: 0.08em;
    text-transform: uppercase;
}

.brand-copy strong {
    font-size: 14px;
    font-weight: 600;
}

.primary-nav {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 6px;
    min-width: 0;
    overflow-x: auto;
    scrollbar-width: none;
}

.primary-nav::-webkit-scrollbar {
    display: none;
}

.nav-item {
    flex: 0 0 auto;
    min-height: 32px;
    padding: 0 12px;
    font-size: 14px;
    font-weight: 500;
    border-radius: var(--radius-sm);
    color: var(--color-text-muted);
}

.nav-item.is-active {
    box-shadow: none;
    color: var(--color-text);
}

.nav-controls {
    display: inline-flex;
    align-items: center;
    justify-content: flex-end;
    gap: 8px;
    min-width: max-content;
}

.tabs-language-switcher {
    --lang-icon-size: 1.15em;
    --lang-icon-color: var(--admin-theme-toggle-color);
    border-radius: 999px;
}

@media (max-width: 860px) {
    .dashboard-shell-nav {
        grid-template-columns: 1fr auto;
        grid-template-areas:
            "brand controls"
            "nav nav";
    }

    .brand-pill { grid-area: brand; width: fit-content; }
    .primary-nav { grid-area: nav; justify-content: flex-start; padding-bottom: 2px; }
    .nav-controls { grid-area: controls; }
}

@media (max-width: 560px) {
    .dashboard-shell-nav {
        gap: 10px;
    }

    .brand-copy strong {
        max-width: 120px;
        overflow: hidden;
        text-overflow: ellipsis;
    }

    .nav-item {
        flex: 1 0 auto;
        padding: 0 12px;
        font-size: 13px;
    }
}
</style>
