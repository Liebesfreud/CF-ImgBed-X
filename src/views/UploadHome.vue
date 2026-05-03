<template>
    <div class="container">
    <div class="upload-home">
        <header class="app-topbar">
            <div class="header-content">
                <DashboardTabs activeTab="home">
                    <template #actions>
                        <BaseButton class="nav-action danger" icon="sign-out-alt" variant="ghost" size="md" aria-label="Logout" @click="handleLogout" />
                    </template>
                </DashboardTabs>
            </div>
        </header>

        <section class="upload-workspace-panel">
            <div class="upload-folder-container topbar-folder" :class="{ 'no-announcement': !announcementAvailable }">
                <div class="upload-folder" :class="{ 'active': isFolderInputActive }">
                    <DirectorySuggestionInput
                        v-if="showDirectorySuggestions"
                        v-model="uploadFolder"
                        class="inner-folder-input"
                        :placeholder="$t('upload.folderPlaceholder')"
                        @focus="handleFolderInputFocus"
                        @blur="handleFolderInputBlur"
                        @select="handleDirectorySelect"
                    />
                    <el-input
                        v-else
                        class="inner-folder-input"
                        v-model="uploadFolder"
                        :placeholder="$t('upload.folderPlaceholder')"
                        @focus="handleFolderInputFocus"
                        @blur="handleFolderInputBlur"
                    />
                </div>
                <DirectoryTreePicker
                    v-if="showDirectorySuggestions"
                    :current-directory="uploadFolder"
                    source="upload"
                    @select="handleDirectorySelect"
                >
                    <template #trigger>
                        <el-button class="directory-tree-trigger">
                            <font-awesome-icon icon="folder-tree" />
                        </el-button>
                    </template>
                </DirectoryTreePicker>
            </div>

            <div class="upload-page-actions ui-scroll-actions" :aria-label="$t('upload.settings')">
                <BaseButton class="page-action primary" :icon="uploadMethod === 'default' ? 'paste' : 'folder-open'" variant="secondary" @click="handleChangeUploadMethod">
                    <font-awesome-icon :icon="uploadMethod === 'default' ? 'paste' : 'folder-open'" />
                    {{ uploadMethod === 'default' ? $t('upload.pasteUpload') : $t('upload.fileUpload') }}
                </BaseButton>
                <BaseButton class="page-action" icon="cloud-upload" variant="secondary" @click="openCompressDialog">
                    {{ $t('upload.settings') }}
                </BaseButton>
                <BaseButton class="page-action" icon="link" variant="secondary" @click="openUrlDialog">
                    {{ $t('upload.linkFormat') }}
                </BaseButton>
                <BaseButton class="page-action" icon="history" variant="secondary" @click="showHistory = true">
                    {{ $t('upload.history') }}
                </BaseButton>
                <BaseButton class="page-action" icon="bullhorn" variant="secondary" :disabled="!announcementAvailable" @click="handleShowAnnouncement">
                    {{ $t('upload.announcement') }}
                </BaseButton>
            </div>
        </section>
        <div class="header">
            <h1 class="title"><a class="main-title" href="https://github.com/MarSeventh/CloudFlare-ImgBed" target="_blank">{{ ownerName }}</a> ImgHub</h1>
        </div>
        <UploadForm 
            :selectedUrlForm="selectedUrlForm" 
            :customerCompress="customerCompress" 
            :compressQuality="compressQuality"
            :compressBar="compressBar"
            :serverCompress="serverCompress"
            :uploadChannel="uploadChannel"
            :channelName="channelName"
            :uploadNameType="uploadNameType"
            :useCustomUrl="useCustomUrl"
            :customUrlPrefix="customUrlPrefix"
            :autoRetry="autoRetry"
            :urlPrefix="urlPrefix"
            :uploadMethod="uploadMethod"
            :uploadFolder="uploadFolder"
            :convertToWebp="convertToWebp"
            class="upload"
        />
        <el-dialog :title="$t('settings.linkFormatTitle')" v-model="showUrlDialog" :width="dialogWidth" :show-close="false" class="settings-dialog">
            <div class="dialog-section">
                <div class="section-header">
                    <span class="section-title">{{ $t('settings.defaultCopyLink') }}</span>
                </div>
                <div class="section-content">
                    <el-radio-group v-model="selectedUrlForm" @change="changeUrlForm" class="radio-card-group grid-2x2">
                        <el-radio value="url" class="radio-card">
                            <font-awesome-icon icon="link" class="radio-icon"/>
                            <span>{{ $t('settings.rawLink') }}</span>
                        </el-radio>
                        <el-radio value="md" class="radio-card">
                            <font-awesome-icon icon="code" class="radio-icon"/>
                            <span>MarkDown</span>
                        </el-radio>
                        <el-radio value="html" class="radio-card">
                            <font-awesome-icon icon="code-branch" class="radio-icon"/>
                            <span>HTML</span>
                        </el-radio>
                        <el-radio value="ubb" class="radio-card">
                            <font-awesome-icon icon="quote-right" class="radio-icon"/>
                            <span>BBCode</span>
                        </el-radio>
                    </el-radio-group>
                </div>
            </div>
            
            <div class="dialog-section">
                <div class="section-header">
                    <span class="section-title">{{ $t('settings.customLink') }}</span>
                    <el-tooltip :content="$t('settings.customLinkTooltip')" placement="top" raw-content>
                        <font-awesome-icon icon="question-circle" class="section-help-icon"/>
                    </el-tooltip>
                </div>
                <div class="section-content">
                    <div class="setting-item">
                        <span class="setting-label">{{ $t('settings.enableCustom') }}</span>
                        <el-switch v-model="useCustomUrl" active-value="true" inactive-value="false" />
                    </div>
                    <div class="setting-item" v-if="useCustomUrl === 'true'">
                        <span class="setting-label">{{ $t('settings.customPrefix') }}</span>
                        <el-input v-model="customUrlPrefix" :placeholder="$t('settings.customPrefixPlaceholder')" class="setting-input"/>
                    </div>
                </div>
            </div>
            
            <div class="dialog-action">
                <el-button type="primary" @click="showUrlDialog = false" class="confirm-btn">{{ $t('settings.confirm') }}</el-button>
            </div>
        </el-dialog>
        <UploadSettingsDialog
            v-model="showCompressDialog"
            v-model:uploadChannel="uploadChannel"
            v-model:channelName="channelName"
            :currentChannelList="currentChannelList"
            v-model:uploadFolder="uploadFolder"
            v-model:autoRetry="autoRetry"
            v-model:uploadNameType="uploadNameType"
            v-model:convertToWebp="convertToWebp"
            v-model:customerCompress="customerCompress"
            v-model:compressBar="compressBar"
            v-model:compressQuality="compressQuality"
            v-model:serverCompress="serverCompress"
        />
    </div>
    <Footer class="footer"/>
    <el-dialog :title="$t('upload.announcementTitle')" v-model="showAnnouncementDialog" :width="dialogWidth" :show-close="false" :close-on-click-modal="false" :close-on-press-escape="false" center>
        <div v-html="announcementContent"></div>
        <template #footer>
            <span class="dialog-footer">
                <el-button type="primary" @click="showAnnouncementDialog = false">{{ $t('upload.announcementAck') }}</el-button>
            </span>
        </template>
    </el-dialog>
    <UploadHistory :show="showHistory" @close="showHistory = false" />
    </div>
</template>

<script>
import UploadForm from '@/components/upload/UploadForm.vue'
import Footer from '@/components/Footer.vue'
import Logo from '@/components/Logo.vue'
import DashboardTabs from '@/components/DashboardTabs.vue'
import UploadHistory from '@/components/upload/UploadHistory.vue'
import UploadSettingsDialog from '@/components/upload/UploadSettingsDialog.vue'
import DirectoryTreePicker from '@/components/DirectoryTreePicker.vue'
import DirectorySuggestionInput from '@/components/DirectorySuggestionInput.vue'
import backgroundManager from '@/mixins/backgroundManager'
import axios from '@/utils/axios'
import { ref } from 'vue'
import { mapGetters } from 'vuex'
import { validateFolderPath } from '@/utils/pathValidator'

export default {
    name: 'UploadHome',
    mixins: [backgroundManager],
    data() {
        return {
            selectedUrlForm: ref(''),
            showUrlDialog: false,
            showCompressDialog: false,
            customerCompress: true, //涓婁紶鍓嶅帇缂?
            compressQuality: 4, //鍘嬬缉鍚庡ぇ灏?
            compressBar: 5, //鍘嬬缉闃堝€?
            convertToWebp: false, //杞崲涓篧ebP鏍煎紡
            serverCompress: true, //鏈嶅姟鍣ㄧ鍘嬬缉
            uploadChannel: '', //涓婁紶娓犻亾
            channelName: '', //鎸囧畾鐨勬笭閬撳悕绉?
            availableChannels: {}, //鍙敤娓犻亾鍒楄〃
            uploadNameType: '', //涓婁紶鏂囦欢鍛藉悕鏂瑰紡
            customUrlPrefix: '', //鑷畾涔夐摼鎺ュ墠缂€
            useCustomUrl: 'false', //鏄惁鍚敤鑷畾涔夐摼鎺ユ牸寮?
            autoRetry: true, //澶辫触鑷姩鍒囨崲
            useDefaultWallPaper: false,
            uploadMethod: 'default', //涓婁紶鏂瑰紡
            uploadFolder: '', // 涓婁紶鏂囦欢澶?
            isFolderInputActive: false,
            showAnnouncementDialog: false, // 鎺у埗鍏憡寮圭獥鐨勬樉绀?
            announcementContent: '', // 鍏憡鍐呭
            showHistory: false,
        }
    },
    watch: {
        customerCompress(val) {
            this.updateCompressConfig('customerCompress', val)
        },
        compressQuality(val) {
            this.updateCompressConfig('compressQuality', val)
        },
        compressBar(val) {
            // 纭繚鍊煎湪鏈夋晥鑼冨洿鍐?
            if (val === null || val === undefined || val < 1) {
                this.compressBar = 1
                return
            }
            // 纭繚鏈熸湜澶у皬涓嶈秴杩囧帇缂╅槇鍊?
            if (this.compressQuality > val) {
                this.compressQuality = val
            }
            this.updateCompressConfig('compressBar', val)
        },
        serverCompress(val) {
            this.updateCompressConfig('serverCompress', val)
        },
        convertToWebp(val) {
            this.updateCompressConfig('convertToWebp', val)
        },
        uploadChannel(val) {
            this.updateStoreUploadChannel(val)
            // 鍒囨崲娓犻亾绫诲瀷鏃讹紝妫€鏌ユ寔涔呭寲鐨勬笭閬撳悕鏄惁鍦ㄦ柊娓犻亾鍒楄〃涓?
            const newChannelList = this.availableChannels[val] || []
            const savedChannelName = this.storeChannelName
            if (savedChannelName && newChannelList.some(ch => ch.name === savedChannelName)) {
                // 鎸佷箙鍖栫殑娓犻亾鍚嶅湪鏂版笭閬撳垪琛ㄤ腑锛屾仮澶嶅畠
                this.channelName = savedChannelName
            } else {
                // 鍚﹀垯娓呯┖
                this.channelName = ''
            }
        },
        channelName(val) {
            // 纭繚娓呯┖鏃朵繚瀛樼┖瀛楃涓茶€屼笉鏄痭ull
            this.$store.commit('setStoreChannelName', val || '')
        },
        uploadNameType(val) {
            this.updateStoreUploadNameType(val)
        },
        customUrlPrefix(val) {
            this.$store.commit('setCustomUrlSettings', { key: 'customUrlPrefix', value: val })
        },
        useCustomUrl(val) {
            this.$store.commit('setCustomUrlSettings', { key: 'useCustomUrl', value: val })
        },
        autoRetry(val) {
            this.$store.commit('setStoreAutoRetry', val)
        },
        uploadFolder(val) {
            // 瀹炴椂杈撳叆鏃剁敤闈?strict 妯″紡锛屼笉妫€鏌ユ湯灏剧殑鍗曠嫭 . 浠ュ厑璁哥户缁緭鍏ュ .123
            if (this.validateUploadFolder(val, false)) {
                // 闈?strict 閫氳繃鍚庯紝鍐嶇敤 strict 妯″紡闈欓粯妫€鏌ワ紝鍙湁瀹屽叏鍚堟硶鎵嶆洿鏂?store
                const strictResult = validateFolderPath(val, { strict: true })
                if (strictResult.valid) {
                    this.$store.commit('setStoreUploadFolder', val)
                }
                // strict 涓嶉€氳繃鏃朵笉鏇存柊 store锛岀瓑澶辩劍鏃舵彁绀哄苟鍥炴粴
            } else {
                this.$nextTick(() => {
                    this.uploadFolder = this.storeUploadFolder
                })
            }
        }
    },
    computed: {
        ...mapGetters(['userConfig', 'uploadCopyUrlForm', 'compressConfig', 'storeUploadChannel', 'storeChannelName', 'storeUploadNameType', 'customUrlSettings', 'storeAutoRetry', 'storeUploadMethod', 'storeUploadFolder']),
        ownerName() {
            return this.userConfig?.ownerName || 'Sanyue'
        },
        dialogWidth() {
            return window.innerWidth > 768 ? '50%' : '90%'
        },
        disableTooltip() {
            return window.innerWidth < 768
        },
        urlPrefix() {
            // 鍏ㄥ眬鑷畾涔夐摼鎺ュ墠缂€
            return this.userConfig?.urlPrefix || `${window.location.protocol}//${window.location.host}/file/`
        },
        announcementAvailable() {
            return !!this.userConfig?.announcement
        },
        // 鏄惁鏄剧ず鐩綍鍊欓€夐」锛堜粠 userConfig 鑾峰彇锛?
        showDirectorySuggestions() {
            return this.userConfig?.showDirectorySuggestions ?? false
        },
        // 褰撳墠娓犻亾绫诲瀷瀵瑰簲鐨勬笭閬撳垪琛?
        currentChannelList() {
            return this.availableChannels[this.uploadChannel] || []
        }
    },
    mounted() {
        // 鍒濆鍖栬儗鏅浘锛屽惎鐢ㄨ嚜鍔ㄥ垱寤哄厓绱?
        this.initializeBackground('uploadBkImg', '.container', false, true)

        // 璇诲彇鐢ㄦ埛閫夋嫨鐨勯摼鎺ユ牸寮?
        this.selectedUrlForm = this.uploadCopyUrlForm || 'url'
        // 璇诲彇鐢ㄦ埛閫夋嫨鐨勫帇缂╄缃紙浼樺厛鐢ㄦ埛璁剧疆锛屽叾娆＄郴缁熼粯璁ら厤缃級
        this.customerCompress = this.compressConfig.customerCompress ?? this.parseBoolean(this.userConfig?.defaultCustomerCompress, true)
        this.compressQuality = this.compressConfig.compressQuality ?? this.parseNumber(this.userConfig?.defaultCompressQuality, 4)
        this.compressBar = this.compressConfig.compressBar ?? this.parseNumber(this.userConfig?.defaultCompressBar, 5)
        this.serverCompress = this.compressConfig.serverCompress ?? true
        this.convertToWebp = this.compressConfig.convertToWebp ?? this.parseBoolean(this.userConfig?.defaultConvertToWebp, false)
        // 璇诲彇鐢ㄦ埛閫夋嫨鐨勪笂浼犳笭閬?
        this.uploadChannel = this.storeUploadChannel || this.userConfig?.defaultUploadChannel || 'telegram'
        // 鐢ㄦ埛瀹氫箟鐨勫け璐ヨ嚜鍔ㄥ垏鎹?
        this.autoRetry = this.storeAutoRetry
        // 璇诲彇鐢ㄦ埛閫夋嫨鐨勪笂浼犳枃浠跺懡鍚嶆柟寮?
        this.uploadNameType = this.storeUploadNameType || this.userConfig?.defaultUploadNameType || 'default'
        // 璇诲彇鐢ㄦ埛鑷畾涔夐摼鎺ユ牸寮?
        this.customUrlPrefix = this.customUrlSettings.customUrlPrefix
        this.useCustomUrl = this.customUrlSettings.useCustomUrl
        // 璇诲彇鐢ㄦ埛鍋忓ソ鐨勪笂浼犳柟寮?
        this.uploadMethod = this.storeUploadMethod
        // 鑾峰彇鍙敤娓犻亾鍒楄〃
        this.fetchAvailableChannels()
        // 璇诲彇鐢ㄦ埛璁剧疆鐨勪笂浼犳枃浠跺す
        this.uploadFolder = this.storeUploadFolder || this.userConfig?.defaultUploadFolder || ''

        // 棣栨璁块棶鍏憡
        const visited = localStorage.getItem('visitedUploadHome')
        const announcement = this.userConfig?.announcement
        if (!visited && announcement) {
            this.announcementContent = announcement
            this.showAnnouncementDialog = true
            localStorage.setItem('visitedUploadHome', 'true')
        }
    },
    components: {
        UploadForm,
        Footer,
        Logo,
        DashboardTabs,
        UploadHistory,
        UploadSettingsDialog,
        DirectoryTreePicker,
        DirectorySuggestionInput
    },
    methods: {
        // 鑾峰彇鍙敤娓犻亾鍒楄〃
        async fetchAvailableChannels() {
            try {
                const response = await axios.get('/api/channels', { withAuthCode: true })
                if (response.data) {
                    this.availableChannels = response.data
                    // 鎭㈠娓犻亾鍚嶇О锛氫紭鍏堟寔涔呭寲鐨勫€硷紝鍏舵绯荤粺榛樿閰嶇疆
                    const savedChannelName = this.storeChannelName
                    const defaultChannelName = this.userConfig?.defaultChannelName
                    const currentChannelList = this.availableChannels[this.uploadChannel] || []
                    
                    // 濡傛灉鐢ㄦ埛涓诲姩娓呯┖杩囷紙savedChannelName === ''锛夛紝鍒欎繚鎸佷负绌?
                    // 濡傛灉浠庢湭閫夋嫨杩囷紙savedChannelName === null/undefined锛夛紝鍒欎娇鐢ㄩ粯璁ゅ€?
                    if (savedChannelName && currentChannelList.some(ch => ch.name === savedChannelName)) {
                        this.channelName = savedChannelName
                    } else if (savedChannelName === '' || savedChannelName === null || savedChannelName === undefined) {
                        // 鐢ㄦ埛涓诲姩娓呯┖鎴栦粠鏈€夋嫨锛屾鏌ユ槸鍚︿娇鐢ㄩ粯璁ゅ€?
                        if (savedChannelName !== '' && defaultChannelName && currentChannelList.some(ch => ch.name === defaultChannelName)) {
                            this.channelName = defaultChannelName
                        }
                        // 濡傛灉 savedChannelName === ''锛岃鏄庣敤鎴蜂富鍔ㄦ竻绌猴紝淇濇寔涓虹┖
                    }
                }
            } catch (error) {
                console.error('Failed to fetch available channels:', error)
            }
        },
        // 楠岃瘉涓婁紶鏂囦欢澶硅矾寰勭殑鍚堟硶鎬?
        validateUploadFolder(path, strict = true) {
            // 鑷姩琛ュ叏鍓嶅 /
            if (path && !path.startsWith('/')) {
                path = '/' + path
                this.uploadFolder = path
            }
            const result = validateFolderPath(path, { strict })
            if (!result.valid) {
                this.$message.error(result.error)
                return false
            }
            return true
        },
        handleFolderInputFocus() {
            this.isFolderInputActive = true
        },
        handleFolderInputBlur() {
            this.isFolderInputActive = false
            // 澶辩劍鏃惰嚜鍔ㄨˉ鍏ㄥ墠瀵?/
            if (this.uploadFolder && !this.uploadFolder.startsWith('/')) {
                this.uploadFolder = '/' + this.uploadFolder
            }
            // 澶辩劍鏃跺仛瀹屾暣鏍￠獙锛堝寘鎷湯灏惧崟鐙殑 .锛?
            if (!this.validateUploadFolder(this.uploadFolder, true)) {
                this.$nextTick(() => {
                    this.uploadFolder = this.storeUploadFolder
                })
            }
        },
        handleManage() {
            this.$router.push('/dashboard')
        },
        // 瑙ｆ瀽甯冨皵鍊?
        parseBoolean(value, defaultValue) {
            if (value === undefined || value === null) return defaultValue
            if (typeof value === 'boolean') return value
            if (typeof value === 'string') return value === 'true'
            return defaultValue
        },
        // 瑙ｆ瀽鏁板瓧
        parseNumber(value, defaultValue) {
            if (value === undefined || value === null) return defaultValue
            const num = parseFloat(value)
            return isNaN(num) ? defaultValue : num
        },
        openUrlDialog() {
            this.showUrlDialog = true
        },
        handleLogout() {
            axios.post('/api/auth/logout', { authType: 'user' }, { withCredentials: true }).finally(() => {
                this.$store.commit('setUserLoggedIn', false);
                this.$router.push('/login')
                this.$message.success(this.$t('upload.logoutSuccess'))
            })
        },
        changeUrlForm() {
            this.$store.commit('setUploadCopyUrlForm', this.selectedUrlForm)
        },
        openCompressDialog() {
            this.showCompressDialog = true
        },
        updateCompressConfig(key, value) {
            this.$store.commit('setCompressConfig', { key, value })
        },
        updateStoreUploadChannel(value) {
            this.$store.commit('setStoreUploadChannel', value)
        },
        updateStoreUploadNameType(value) {
            this.$store.commit('setStoreUploadNameType', value)
        },
        handleChangeUploadMethod() {
            this.uploadMethod = this.uploadMethod === 'default'? 'paste' : 'default'
            this.$store.commit('setUploadMethod', this.uploadMethod)
        },
        handleDesktopMenuCommand(command) {
            if (command === 'viewDocs') {
                window.open('https://cfbed.sanyue.de/qa/', '_blank')
            }
        },
        handleShowAnnouncement() {
            const announcement = this.userConfig?.announcement
            if (announcement) {
                this.announcementContent = announcement
                this.showAnnouncementDialog = true
            } else {
                this.$message.info(this.$t('upload.noAnnouncement'))
            }
        },
        // 澶勭悊鐩綍閫夋嫨
        handleDirectorySelect(path) {
            // 濉叆閫夋嫨鐨勭洰褰曡矾寰?
            this.uploadFolder = path
            // 瑙﹀彂璺緞楠岃瘉閫昏緫
            if (this.validateUploadFolder(path, true)) {
                this.$store.commit('setStoreUploadFolder', path)
            }
        }
    }
}
</script>

<style scoped>
.container {
    background: var(--bg-color);
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

.upload-workspace-panel {
    display: grid;
    grid-template-columns: minmax(220px, 360px) minmax(0, 1fr);
    align-items: center;
    gap: var(--space-3);
    width: min(1080px, calc(100% - 32px));
    margin: 0 auto var(--space-6);
    padding: 8px 12px;
    border-radius: var(--radius-xl);
    background: transparent;
    box-shadow: none;
}

.upload-folder-container {
    display: flex;
    align-items: center;
    min-width: 0;
}

.upload-folder {
    width: 100%;
    height: 38px;
    border-radius: 999px;
    transition: box-shadow var(--motion-base) var(--motion-ease);
}

.upload-folder :deep(.inner-folder-input),
.upload-folder :deep(.el-input) {
    width: 100%;
    height: 100%;
}

.upload-folder :deep(.el-input__wrapper) {
    height: 100%;
    border-radius: 999px;
    background-color: var(--color-surface);
    box-shadow: var(--shadow-as-border);
    border: none;
}

.upload-folder :deep(.el-input__inner) {
    font-size: 14px;
}

.directory-tree-trigger {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    width: var(--control-height-lg);
    height: var(--control-height-lg);
    margin-left: var(--space-2);
    border: none;
    border-radius: 999px;
    background-color: var(--color-surface);
    color: var(--color-text);
    box-shadow: var(--shadow-as-border);
}

.upload-page-actions {
    justify-content: flex-end;
}


@media (max-width: 980px) {
    .upload-workspace-panel {
        grid-template-columns: 1fr;
    }

    .upload-page-actions {
        justify-content: flex-start;
    }
}

@media (max-width: 560px) {
    .app-topbar {
        width: calc(100% - 16px);
        top: 6px;
        border-radius: 18px;
        padding: 8px;
    }

    .upload-workspace-panel {
        width: calc(100% - 20px);
        border-radius: 18px;
    }

    .upload-page-actions :deep(.base-button) {
        min-height: 34px;
        padding: 0 10px;
        font-size: 12px;
    }

    .upload-page-actions :deep(.base-button__label) {
        display: none;
    }
}

:deep(.el-dialog) {
    border-radius: 12px;
    background-color: var(--dialog-bg-color);
box-shadow: var(--dialog-box-shadow);
}
.dialog-action {
    display: flex;
    justify-content: center;
    margin-top: 20px;
}


.header {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 24px 15px 10px;
    margin-top: 0;
    color: var(--upload-header-color);
    user-select: none;
    text-decoration: none;
    position: relative;
}
.title {
    font-size: clamp(2.2rem, 5vw, 4.5rem);
    font-weight: 650;
    position: relative;
    padding-bottom: 10px;
    cursor: pointer;
    letter-spacing: -0.06em;
    line-height: 0.95;
    text-wrap: balance;
}
.title::after {
    content: '';
    position: absolute;
    bottom: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 72%;
    height: 1px;
    background: linear-gradient(90deg, transparent, rgba(0, 0, 0, 0.18), transparent);
}

.main-title {
    color: var(--upload-main-title-color);
    text-decoration: none;
    display: inline-block;
    position: relative;
}

@media (max-width: 768px) {
    .title {
        font-size: 2.25rem;
        letter-spacing: -0.045em;
    }
}

.upload-home {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    min-height: 94vh;
    background:
        radial-gradient(circle at 50% 0%, rgba(10, 114, 239, 0.05), transparent 30%),
        var(--admin-container-bg-color);
}
.upload {
    margin-bottom: 5px;
    position: relative;
}

.footer {
    height: 6vh;
}
</style>


