<template>
    <div class="page-index">
        <h2>LAVAS</h2>
        <h4>[ˈlɑ:vəz]</h4>
        <div class="demo">
            <p>点击图片放大预览</p>
            <ImageZoom src="/static/img/icons/android-chrome-512x512.png" alt="Logo 预览" />
        </div>
    </div>
</template>

<script>
function setState(store) {}

export default {
    name: 'index',
    components: {
        ImageZoom: require('../components/ImageZoom.vue').default
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

    .demo
        margin-top 24px
        text-align center
        p
            margin 0 0 12px
            color #666

</style>
