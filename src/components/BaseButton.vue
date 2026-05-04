<template>
    <component
        :is="tag"
        class="base-button"
        :class="buttonClasses"
        :type="computedType"
        :href="href"
        :target="target"
        :rel="computedRel"
        :disabled="isNativeButton ? disabled || loading : null"
        :aria-disabled="!isNativeButton && (disabled || loading) ? 'true' : null"
        :aria-label="ariaLabel"
        @click="handleClick"
    >
        <span v-if="loading" class="base-button__spinner" aria-hidden="true"></span>
        <span v-else-if="$slots.icon || icon" class="base-button__icon" aria-hidden="true">
            <slot name="icon">
                <font-awesome-icon v-if="icon" :icon="icon" />
            </slot>
        </span>
        <span v-if="$slots.default" class="base-button__label">
            <slot />
        </span>
    </component>
</template>

<script>
export default {
    name: 'BaseButton',
    props: {
        variant: {
            type: String,
            default: 'secondary',
            validator: value => ['primary', 'secondary', 'ghost', 'danger', 'success', 'warning', 'info', 'text'].includes(value)
        },
        size: {
            type: String,
            default: 'md',
            validator: value => ['sm', 'md', 'lg'].includes(value)
        },
        type: {
            type: String,
            default: 'button'
        },
        href: {
            type: String,
            default: ''
        },
        target: {
            type: String,
            default: null
        },
        rel: {
            type: String,
            default: null
        },
        icon: {
            type: [String, Array, Object],
            default: null
        },
        loading: {
            type: Boolean,
            default: false
        },
        disabled: {
            type: Boolean,
            default: false
        },
        block: {
            type: Boolean,
            default: false
        },
        round: {
            type: Boolean,
            default: false
        },
        circle: {
            type: Boolean,
            default: false
        },
        ariaLabel: {
            type: String,
            default: null
        }
    },
    emits: ['click'],
    computed: {
        tag() {
            return this.href ? 'a' : 'button';
        },
        isNativeButton() {
            return this.tag === 'button';
        },
        computedType() {
            return this.isNativeButton ? this.type : null;
        },
        computedRel() {
            if (this.rel) return this.rel;
            return this.target === '_blank' ? 'noopener noreferrer' : null;
        },
        buttonClasses() {
            return [
                `base-button--${this.variant}`,
                `base-button--${this.size}`,
                {
                    'base-button--block': this.block,
                    'base-button--round': this.round || this.circle,
                    'base-button--square': this.circle || !this.$slots.default,
                    'is-loading': this.loading,
                    'is-disabled': this.disabled || this.loading
                }
            ];
        }
    },
    methods: {
        handleClick(event) {
            if (this.disabled || this.loading) {
                event.preventDefault();
                return;
            }
            this.$emit('click', event);
        }
    }
};
</script>
