<template>
    <div class="move-file-dialog-wrapper">
        <!-- 桌面端使用 Dialog -->
        <el-dialog
            v-if="isDesktop"
            v-model="dialogVisible"
            :title="isBatchMove ? $t('moveFile.batchTitle') : $t('moveFile.title')"
            width="500px"
            :show-close="true"
            class="move-dialog"
            @close="handleClose"
        >
            <div class="move-dialog-content">
                <div class="move-path-input-container">
                    <el-input
                        v-model="targetPath"
                        :placeholder="$t('moveFile.targetPlaceholder')"
                        class="move-path-input"
                    >
                        <template #prepend>{{ $t('moveFile.targetDirectory') }}</template>
                    </el-input>
                    <DirectoryTreePicker
                        :current-directory="currentDirectory"
                        source="admin"
                        @select="handleDirectorySelect"
                    >
                        <template #trigger>
                            <el-button class="directory-picker-trigger">
                                <font-awesome-icon icon="folder-tree" />
                            </el-button>
                        </template>
                    </DirectoryTreePicker>
                </div>
                <p class="move-dialog-hint">{{ $t('moveFile.hint') }}</p>
            </div>
            <template #footer>
                <span class="dialog-footer">
                    <BaseButton @click="handleClose" variant="secondary">{{ $t('moveFile.cancel') }}</BaseButton>
                    <BaseButton variant="primary" :loading="loading" @click="handleConfirm">{{ $t('moveFile.confirm') }}</BaseButton>
                </span>
            </template>
        </el-dialog>

        <!-- 移动端使用 Drawer -->
        <el-drawer
            v-else
            v-model="dialogVisible"
            :title="isBatchMove ? $t('moveFile.batchTitle') : $t('moveFile.title')"
            direction="btt"
            size="auto"
            :show-close="true"
            class="move-drawer"
            @close="handleClose"
        >
            <div class="move-drawer-content">
                <div class="move-path-section">
                    <div class="move-path-label">{{ $t('moveFile.targetDirectory') }}</div>
                    <div class="move-path-input-row">
                        <el-input
                            v-model="targetPath"
                            :placeholder="$t('moveFile.targetPlaceholder')"
                            class="move-path-input"
                        />
                        <el-button class="directory-picker-trigger" @click="showTreePicker = true">
                            <font-awesome-icon icon="folder-tree" />
                        </el-button>
                    </div>
                    <p class="move-dialog-hint">{{ $t('moveFile.hintMobile') }}</p>
                </div>
                
                <!-- 内嵌目录树 -->
                <div v-if="showTreePicker" class="embedded-tree-section">
                    <div class="tree-section-header">
                        <span class="tree-section-title">{{ $t('moveFile.selectDirectory') }}</span>
                        <el-button text @click="showTreePicker = false">
                            <font-awesome-icon icon="times" />
                        </el-button>
                    </div>
                    <div class="tree-container" v-loading="treeLoading">
                        <div v-if="treeError" class="error-state">
                            <font-awesome-icon icon="exclamation-circle" class="error-icon" />
                            <span class="error-text">{{ treeError }}</span>
                            <el-button type="primary" size="small" @click="fetchDirectoryTree">
                                {{ $t('moveFile.retry') }}
                            </el-button>
                        </div>
                        <el-tree
                            v-else
                            ref="treeRef"
                            :data="treeData"
                            :props="treeProps"
                            node-key="path"
                            :default-expand-all="false"
                            :expand-on-click-node="false"
                            :highlight-current="true"
                            :current-node-key="currentDirectory"
                            class="directory-tree"
                            @node-click="handleTreeNodeClick"
                        >
                            <template #default="{ node, data }">
                                <span class="tree-node" :class="{ 'is-current': data.path === currentDirectory }">
                                    <font-awesome-icon :icon="node.expanded ? 'folder-open' : 'folder'" class="folder-icon" />
                                    <span class="node-label">{{ data.name }}</span>
                                </span>
                            </template>
                        </el-tree>
                    </div>
                </div>
            </div>
            <template #footer>
                <div class="drawer-footer">
                    <BaseButton @click="handleClose" class="footer-btn" variant="secondary">{{ $t('moveFile.cancel') }}</BaseButton>
                    <BaseButton variant="primary" :loading="loading" @click="handleConfirm" class="footer-btn">{{ $t('moveFile.confirm') }}</BaseButton>
                </div>
            </template>
        </el-drawer>
    </div>
</template>

<script>
import DirectoryTreePicker from '@/components/DirectoryTreePicker.vue';
import fetchWithAuth from '@/utils/fetchWithAuth';

export default {
    name: 'MoveFileDialog',
    components: {
        DirectoryTreePicker
    },
    props: {
        modelValue: {
            type: Boolean,
            default: false
        },
        currentDirectory: {
            type: String,
            default: ''
        },
        isBatchMove: {
            type: Boolean,
            default: false
        },
        initialPath: {
            type: String,
            default: '/'
        }
    },
    emits: ['update:modelValue', 'confirm', 'close'],
    data() {
        return {
            targetPath: '/',
            loading: false,
            windowWidth: window.innerWidth,
            // 移动端内嵌树相关
            showTreePicker: false,
            treeData: [],
            treeLoading: false,
            treeError: '',
            treeProps: {
                children: 'children',
                label: 'name'
            }
        };
    },
    computed: {
        dialogVisible: {
            get() {
                return this.modelValue;
            },
            set(val) {
                this.$emit('update:modelValue', val);
            }
        },
        isDesktop() {
            return this.windowWidth > 768;
        }
    },
    watch: {
        modelValue(newVal) {
            if (newVal) {
                this.targetPath = this.initialPath || '/';
                this.showTreePicker = false;
            }
        },
        showTreePicker(newVal) {
            if (newVal && !this.isDesktop) {
                this.fetchDirectoryTree();
            }
        }
    },
    mounted() {
        window.addEventListener('resize', this.handleResize);
    },
    beforeUnmount() {
        window.removeEventListener('resize', this.handleResize);
    },
    methods: {
        handleResize() {
            this.windowWidth = window.innerWidth;
        },
        handleDirectorySelect(path) {
            this.targetPath = path;
        },
        handleConfirm() {
            this.$emit('confirm', this.targetPath);
        },
        handleClose() {
            this.$emit('update:modelValue', false);
            this.$emit('close');
        },
        // 移动端内嵌树相关方法
        async fetchDirectoryTree() {
            this.treeLoading = true;
            this.treeError = '';

            try {
                const response = await fetchWithAuth('/api/directoryTree', {
                    method: 'GET'
                });
                
                if (!response.ok) {
                    if (response.status === 401) {
                        this.treeError = this.$t('moveFile.authFailed');
                    } else {
                        this.treeError = this.$t('moveFile.serverError');
                    }
                    return;
                }
                const data = await response.json();

                if (data.tree) {
                    this.treeData = [data.tree];
                } else {
                    this.treeData = [];
                }
            } catch (err) {
                console.error('Failed to fetch directory tree:', err);
                this.treeError = this.$t('moveFile.loadTreeFailed');
            } finally {
                this.treeLoading = false;
            }
        },
        handleTreeNodeClick(data) {
            let path = data.path;
            if (path && !path.startsWith('/')) {
                path = '/' + path;
            } else if (!path) {
                path = '/';
            }
            this.targetPath = path;
            this.showTreePicker = false;
        }
    }
};
</script>

<style scoped>
.move-dialog-content {
    padding: 0 var(--space-sm);
}

.move-path-input-container {
    display: flex;
    align-items: center;
    gap: var(--space-sm);
}

.move-path-input {
    flex: 1;
}

.directory-picker-trigger {
    flex-shrink: 0;
}

.move-dialog-hint {
    margin-top: var(--space-3);
    font-size: 12px;
    color: var(--color-text-muted);
}

/* 移动端抽屉样式 */
.move-drawer-content {
    padding: var(--space-md);
}

.move-path-section {
    margin-bottom: var(--space-md);
}

.move-path-label {
    font-size: 14px;
    font-weight: 500;
    margin-bottom: var(--space-sm);
    color: var(--color-text);
}

.move-path-input-row {
    display: flex;
    align-items: center;
    gap: var(--space-sm);
}

/* 内嵌目录树样式 */
.embedded-tree-section {
    margin-top: var(--space-md);
    border: 1px solid var(--color-border);
    border-radius: var(--radius-md);
    overflow: hidden;
}

.tree-section-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: var(--space-3) var(--space-md);
    background: var(--color-surface-soft);
    border-bottom: 1px solid var(--color-border);
}

.tree-section-title {
    font-size: 14px;
    font-weight: 500;
}

.tree-container {
    min-height: 200px;
    max-height: 300px;
    overflow-y: auto;
    padding: var(--space-sm);
}

.directory-tree {
    background: transparent;
}

.directory-tree :deep(.el-tree-node__content) {
    height: 44px;
    padding: 0 var(--space-sm);
    border-radius: var(--radius-md);
}

.tree-node {
    display: flex;
    align-items: center;
    gap: var(--space-sm);
    padding: var(--space-xs) 0;
    width: 100%;
}

.tree-node.is-current {
    color: var(--color-accent);
    font-weight: 500;
}

.folder-icon {
    color: var(--color-warning);
    font-size: 16px;
}

.node-label {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}

.error-state {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: var(--space-3);
    padding: var(--space-xl) var(--space-5);
    text-align: center;
}

.error-icon {
    font-size: 32px;
    color: var(--color-danger);
}

.error-text {
    color: var(--color-text-muted);
    font-size: 14px;
}

.drawer-footer {
    display: flex;
    gap: var(--space-3);
    padding: 0 var(--space-md) var(--space-md);
}

.footer-btn {
    flex: 1;
}
</style>

<style>
.move-dialog .el-dialog__body {
    padding: var(--space-5);
}

.move-drawer {
    border-radius: var(--radius-xl) var(--radius-xl) 0 0 !important;
}

.move-drawer .el-drawer__header {
    margin-bottom: 0;
    padding: var(--space-md) var(--space-5);
    border-bottom: 1px solid var(--color-border);
}

.move-drawer .el-drawer__body {
    padding: 0;
}

.move-drawer .el-drawer__footer {
    padding: 0;
}
</style>
