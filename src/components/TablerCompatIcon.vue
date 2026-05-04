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
    IconAlertCircle,
    IconAlertTriangle,
    IconArrowDown,
    IconArrowLeft,
    IconArrowUp,
    IconBan,
    IconBook,
    IconBrandTelegram,
    IconCameraUp,
    IconChartBar,
    IconCheck,
    IconCheckbox,
    IconChevronDown,
    IconChevronsLeft,
    IconChevronsRight,
    IconClock,
    IconCloud,
    IconCloudUpload,
    IconCode,
    IconCopy,
    IconCursorText,
    IconDatabase,
    IconDots,
    IconDotsVertical,
    IconDownload,
    IconExternalLink,
    IconEye,
    IconFile,
    IconFileExport,
    IconFileText,
    IconFilter,
    IconFolder,
    IconFolderOpen,
    IconFolderTree,
    IconGlobe,
    IconHash,
    IconHelpCircle,
    IconHistory,
    IconHome,
    IconInbox,
    IconInfoCircle,
    IconLayoutGrid,
    IconLink,
    IconList,
    IconLogout,
    IconMegaphone,
    IconMenu2,
    IconMessageCircle,
    IconMinus,
    IconMinusVertical,
    IconMoon,
    IconMusic,
    IconPencil,
    IconPhoto,
    IconPlus,
    IconQuote,
    IconRefresh,
    IconRobot,
    IconSearch,
    IconServer,
    IconSettings,
    IconShare,
    IconShield,
    IconShieldCheck,
    IconSortAscendingLetters,
    IconSortDescending,
    IconSparkles,
    IconSun,
    IconSunMoon,
    IconTag,
    IconTags,
    IconTool,
    IconTrash,
    IconUpload,
    IconUserCog,
    IconUserPlus,
    IconX
} from '@tabler/icons-vue';

const iconMap = {
    'adjust': IconAdjustmentsHorizontal,
    'adjustments-horizontal': IconAdjustmentsHorizontal,
    'arrow-down': IconArrowDown,
    'arrow-left': IconArrowLeft,
    'arrow-up': IconArrowUp,
    'bars': IconMenu2,
    'ban': IconBan,
    'book': IconBook,
    'bullhorn': IconMegaphone,
    'camera-up': IconCameraUp,
    'chart-bar': IconChartBar,
    'check': IconCheck,
    'check-square': IconCheckbox,
    'chevron-down': IconChevronDown,
    'clock': IconClock,
    'cloud': IconCloud,
    'cloud-upload': IconCloudUpload,
    'cloud-upload-alt': IconCloudUpload,
    'code': IconCode,
    'code-branch': IconCode,
    'cog': IconSettings,
    'cogs': IconSettings,
    'copy': IconCopy,
    'database': IconDatabase,
    'comments': IconMessageCircle,
    'download': IconDownload,
    'ellipsis-h': IconDots,
    'ellipsis-v': IconDotsVertical,
    'eye': IconEye,
    'exclamation': IconAlertTriangle,
    'exclamation-circle': IconAlertCircle,
    'exclamation-triangle': IconAlertTriangle,
    'external-link-alt': IconExternalLink,
    'file': IconFile,
    'file-alt': IconFileText,
    'file-export': IconFileExport,
    'filter': IconFilter,
    'folder': IconFolder,
    'folder-open': IconFolderOpen,
    'folder-tree': IconFolderTree,
    'globe': IconGlobe,
    'hashtag': IconHash,
    'history': IconHistory,
    'home': IconHome,
    'i-cursor': IconCursorText,
    'image': IconPhoto,
    'images': IconPhoto,
    'inbox': IconInbox,
    'info-circle': IconInfoCircle,
    'link': IconLink,
    'list': IconList,
    'music': IconMusic,
    'minus': IconMinus,
    'minus-square': IconMinusVertical,
    'moon': IconMoon,
    'pager': IconLayoutGrid,
    'paper-plane': IconBrandTelegram,
    'pencil': IconPencil,
    'edit': IconPencil,
    'plus': IconPlus,
    'paste': IconUpload,
    'question-circle': IconHelpCircle,
    'quote-right': IconQuote,
    'redo': IconRefresh,
    'robot': IconRobot,
    'save': IconCheck,
    'search': IconSearch,
    'server': IconServer,
    'share-alt': IconShare,
    'shield': IconShield,
    'shield-alt': IconShieldCheck,
    'sign-out-alt': IconLogout,
    'sort-alpha-up': IconSortAscendingLetters,
    'sort-amount-down': IconSortDescending,
    'spinner': IconRefresh,
    'square': IconCheckbox,
    'sun': IconSun,
    'circle-half-stroke': IconSunMoon,
    'sync': IconRefresh,
    'sync-alt': IconRefresh,
    'tachometer-alt': IconChartBar,
    'tag': IconTag,
    'tags': IconTags,
    'th-large': IconLayoutGrid,
    'tools': IconTool,
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
