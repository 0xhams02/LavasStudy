<template>
    <div class="page-index">
        <h2>LAVAS</h2>
        <h4>[ˈlɑ:vəz]</h4>

        <div class="demo-row">
            <image-preview
                :src="thumb192"
                :preview-src="full512"
                alt="示例图片（点击放大）"
            ></image-preview>
        </div>
    </div>
    
</template>

<script>
function setState(store) {}

import ImagePreview from '@/components/ImagePreview';

export default {
    name: 'index',
    components: {
        ImagePreview
    },
    metaInfo: {
        title: 'Home',
        titleTemplate: '%s - Lavas',
        meta: [
            {name: 'keywords', content: 'lavas PWA'},
            {name: 'description', content: '基于 Vue 的 PWA 解决方案，帮助开发者快速搭建 PWA 应用，解决接入 PWA 的各种问题'}
        ]
    },
    async asyncData({store, route}) {
        setState(store);
    },
    data() {
        return {
            thumb192: '/static/img/icons/android-chrome-192x192.png',
            full512: '/static/img/icons/android-chrome-512x512.png'
        };
    }
};
</script>

<style lang="stylus" scoped>

.page-index
    display flex
    justify-content center
    align-items center
    flex-direction column

    h2
        font-size 46px
        font-weight 500
        margin-bottom 0

</style>
