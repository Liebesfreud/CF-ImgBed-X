<template>
    <transition name="fade-down">
        <button
            v-show="visible"
            class="floating-save-btn"
            :class="{ 'is-loading': loading }"
            type="button"
            @click="handleClick"
        >
            <IconLoader2 v-if="loading" class="save-icon is-spin" :size="18" :stroke-width="1.9" />
            <IconDeviceFloppy v-else class="save-icon" :size="18" :stroke-width="1.9" />
            <span class="save-text">{{ loading ? $t('floatingSave.saving') : $t('floatingSave.save') }}</span>
        </button>
    </transition>
</template>

<script>
import { IconDeviceFloppy, IconLoader2 } from '@tabler/icons-vue';

export default {
    name: 'FloatingSaveButton',
    components: {
        IconDeviceFloppy,
        IconLoader2
    },
    emits: ['click'],
    props: {
        loading: {
            type: Boolean,
            default: false
        },
        show: {
            type: Boolean,
            default: true
        }
    },
    data() {
        return {
            visible: false
        };
    },
    watch: {
        show: {
            immediate: true,
            handler(val) {
                if (val) {
                    setTimeout(() => {
                        this.visible = true;
                    }, 250);
                } else {
                    this.visible = false;
                }
            }
        }
    },
    methods: {
        handleClick() {
            if (!this.loading) {
                this.$emit('click');
            }
        }
    }
};
</script>

<style scoped>
.floating-save-btn {
    position: fixed;
    top: var(--floating-btn-top, 76px);
    right: var(--floating-btn-right, 32px);
    bottom: auto;
    display: inline-flex;
    align-items: center;
    gap: 8px;
    min-height: 38px;
    padding: 0 16px;
    border: 1px solid var(--color-accent);
    background: var(--floating-btn-bg);
    color: var(--floating-btn-color);
    border-radius: var(--radius-sm);
    cursor: pointer;
    box-shadow: var(--floating-btn-shadow);
    transition: transform var(--motion-base) var(--motion-ease), box-shadow var(--motion-base) var(--motion-ease), opacity var(--motion-base) var(--motion-ease);
    z-index: 2003;
    font-family: inherit;
    font-weight: 700;
    font-size: 13px;
    user-select: none;
}

.floating-save-btn:hover {
    transform: translateY(-1px);
    box-shadow: var(--floating-btn-shadow-hover);
}

.floating-save-btn:active {
    transform: translateY(0);
}

.floating-save-btn.is-loading {
    cursor: progress;
    opacity: 0.84;
}

.save-icon {
    flex-shrink: 0;
}

.is-spin {
    animation: save-spin 1s linear infinite;
}

.fade-down-enter-active,
.fade-down-leave-active {
    transition: opacity var(--motion-base) var(--motion-ease), transform var(--motion-base) var(--motion-ease);
}

.fade-down-enter-from,
.fade-down-leave-to {
    opacity: 0;
    transform: translateY(-8px);
}

@keyframes save-spin {
    to { transform: rotate(360deg); }
}

@media (max-width: 980px) {
    .floating-save-btn {
        top: var(--floating-btn-mobile-top, 118px);
        right: 18px;
    }
}

@media (max-width: 480px) {
    .floating-save-btn {
        right: 12px;
        padding: 0 12px;
    }
}
</style>
