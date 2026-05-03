<template>
    <component
        :is="resolvedIcon"
        class="tabler-compat-icon"
        :class="{ 'is-spin': spin }"
        :size="computedSize"
        :stroke-width="strokeWidth"
        aria-hidden="true"
    />
</template>

<script>
import {
    IconAdjustmentsHorizontal,
    IconAlertTriangle,
    IconArrowLeft,
    IconBan,
    IconBook,
    IconBrandTelegram,
    IconChartBar,
    IconCheck,
    IconCheckbox,
    IconChevronDown,
    IconChevronsLeft,
    IconChevronsRight,
    IconCloud,
    IconCloudUpload,
    IconCode,
    IconCopy,
    IconDatabase,
    IconDots,
    IconDotsVertical,
    IconDownload,
    IconExternalLink,
    IconFile,
    IconFileExport,
    IconFolder,
    IconFolderOpen,
    IconFolderTree,
    IconGlobe,
    IconHelpCircle,
    IconHistory,
    IconHome,
    IconLayoutGrid,
    IconLink,
    IconList,
    IconLogout,
    IconMegaphone,
    IconMenu2,
    IconMinus,
    IconMinusVertical,
    IconMoon,
    IconPhoto,
    IconQuote,
    IconRefresh,
    IconSearch,
    IconSettings,
    IconShield,
    IconShieldCheck,
    IconSortAscendingLetters,
    IconSortDescending,
    IconSparkles,
    IconSun,
    IconTags,
    IconTrash,
    IconUpload,
    IconUserCog,
    IconUserPlus,
    IconX
} from '@tabler/icons-vue';

const iconMap = {
    'adjust': IconAdjustmentsHorizontal,
    'adjustments-horizontal': IconAdjustmentsHorizontal,
    'bars': IconMenu2,
    'ban': IconBan,
    'book': IconBook,
    'bullhorn': IconMegaphone,
    'chart-bar': IconChartBar,
    'check': IconCheck,
    'check-square': IconCheckbox,
    'chevron-down': IconChevronDown,
    'cloud': IconCloud,
    'cloud-upload': IconCloudUpload,
    'cloud-upload-alt': IconCloudUpload,
    'code': IconCode,
    'code-branch': IconCode,
    'cog': IconSettings,
    'cogs': IconSettings,
    'copy': IconCopy,
    'database': IconDatabase,
    'download': IconDownload,
    'ellipsis-h': IconDots,
    'ellipsis-v': IconDotsVertical,
    'exclamation': IconAlertTriangle,
    'external-link-alt': IconExternalLink,
    'file': IconFile,
    'file-export': IconFileExport,
    'folder': IconFolder,
    'folder-open': IconFolderOpen,
    'folder-tree': IconFolderTree,
    'globe': IconGlobe,
    'history': IconHistory,
    'home': IconHome,
    'images': IconPhoto,
    'link': IconLink,
    'list': IconList,
    'minus': IconMinus,
    'minus-square': IconMinusVertical,
    'moon': IconMoon,
    'pager': IconLayoutGrid,
    'paper-plane': IconBrandTelegram,
    'paste': IconUpload,
    'question-circle': IconHelpCircle,
    'quote-right': IconQuote,
    'redo': IconRefresh,
    'save': IconCheck,
    'search': IconSearch,
    'shield': IconShield,
    'shield-alt': IconShieldCheck,
    'sign-out-alt': IconLogout,
    'sort-alpha-up': IconSortAscendingLetters,
    'sort-amount-down': IconSortDescending,
    'spinner': IconRefresh,
    'square': IconCheckbox,
    'sun': IconSun,
    'sync': IconRefresh,
    'tachometer-alt': IconChartBar,
    'tags': IconTags,
    'th-large': IconLayoutGrid,
    'times': IconX,
    'trash-alt': IconTrash,
    'upload': IconUpload,
    'user-cog': IconUserCog,
    'user-plus': IconUserPlus
};

export default {
    name: 'TablerCompatIcon',
    props: {
        icon: {
            type: [String, Array],
            default: 'sparkles'
        },
        spin: {
            type: Boolean,
            default: false
        },
        size: {
            type: [String, Number],
            default: undefined
        },
        strokeWidth: {
            type: [String, Number],
            default: 1.8
        }
    },
    computed: {
        iconName() {
            return Array.isArray(this.icon) ? this.icon[this.icon.length - 1] : this.icon;
        },
        resolvedIcon() {
            return iconMap[this.iconName] || IconSparkles;
        },
        computedSize() {
            if (typeof this.size === 'number') return this.size;
            if (this.size === 'lg') return 22;
            if (this.size === 'sm') return 16;
            if (this.size === 'xs') return 14;
            return 20;
        }
    }
};
</script>

<style scoped>
.tabler-compat-icon {
    display: inline-block;
    vertical-align: -0.18em;
    flex-shrink: 0;
}

.is-spin,
.fa-spin {
    animation: tabler-spin 1s linear infinite;
}

@keyframes tabler-spin {
    to { transform: rotate(360deg); }
}
</style>
