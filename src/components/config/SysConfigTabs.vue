<template>
    <div class="sys-config-tabs" role="navigation" :aria-label="$t('dashboardTabs.systemSettings')">
        <button
            v-for="item in menuItems"
            :key="item.index"
            type="button"
            class="sys-tab"
            :class="{ 'is-active': activeIndex === item.index }"
            @click="handleSelect(item.index)"
        >
            <component :is="item.icon" :size="18" :stroke-width="1.8" />
            <span>{{ $t(item.titleKey) }}</span>
        </button>
    </div>
</template>

<script>
import { IconChartBar, IconCloudUpload, IconLayoutGrid, IconSettings, IconShield } from '@tabler/icons-vue';

export default {
    name: 'SysConfigTabs',
    props: {
        activeIndex: {
            type: String,
            default: 'status'
        },
        isCollapse: {
            type: Boolean,
            default: false
        }
    },
    data() {
        return {
            menuItems: [
                { index: 'status', icon: IconChartBar, titleKey: 'sysConfigTabs.systemStatus' },
                { index: 'upload', icon: IconCloudUpload, titleKey: 'sysConfigTabs.uploadSettings' },
                { index: 'security', icon: IconShield, titleKey: 'sysConfigTabs.securitySettings' },
                { index: 'page', icon: IconLayoutGrid, titleKey: 'sysConfigTabs.pageSettings' },
                { index: 'others', icon: IconSettings, titleKey: 'sysConfigTabs.otherSettings' }
            ]
        };
    },
    methods: {
        handleSelect(index) {
            this.$emit('update:activeIndex', index);
            this.$emit('update:isCollapse', false);
        }
    },
    mounted() {
        this.$emit('update:isCollapse', false);
    }
};
</script>

<style scoped>
.sys-config-tabs {
    display: flex;
    align-items: center;
    gap: 6px;
    min-width: 0;
    overflow-x: auto;
    padding: 2px;
    scrollbar-width: none;
}

.sys-config-tabs::-webkit-scrollbar {
    display: none;
}

.sys-tab {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    gap: 7px;
    flex: 0 0 auto;
    min-height: 34px;
    border: 1px solid transparent;
    border-radius: 999px;
    padding: 0 12px;
    background: transparent;
    color: var(--color-text-muted);
    cursor: pointer;
    font-family: inherit;
    font-size: 13px;
    font-weight: 650;
    transition: color var(--motion-fast) var(--motion-ease), background var(--motion-fast) var(--motion-ease), border-color var(--motion-fast) var(--motion-ease);
}

.sys-tab:hover {
    color: var(--color-text);
    background: var(--color-surface-soft);
    border-color: var(--color-border);
}

.sys-tab.is-active {
    color: var(--color-accent-contrast);
    background: var(--color-accent);
    border-color: var(--color-accent);
}

@media (max-width: 768px) {
    .sys-config-tabs {
        width: 100%;
    }

    .sys-tab {
        min-height: 32px;
        padding: 0 10px;
        font-size: 12px;
    }
}
</style>
