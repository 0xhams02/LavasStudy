<template>
    <div class="image-preview">
        <img
            class="image-preview-thumb"
            :src="src"
            :alt="alt"
            @click="openPreview"
        >

        <transition name="fade-zoom">
            <div
                class="image-preview-overlay"
                v-show="isOpen"
                @click.self="closePreview"
            >
                <img
                    class="image-preview-full"
                    :src="previewSrc"
                    :alt="alt"
                    @load="handleFullImageLoaded"
                >
                <button
                    class="image-preview-close"
                    type="button"
                    aria-label="关闭预览"
                    @click="closePreview"
                >×</button>
            </div>
        </transition>
    </div>
</template>

<script>
export default {
    name: 'imagePreview',
    props: {
        src: {
            type: String,
            required: true
        },
        previewSrc: {
            type: String,
            default() {
                return this.src;
            }
        },
        alt: {
            type: String,
            default: ''
        }
    },
    data() {
        return {
            isOpen: false,
            originalBodyOverflow: ''
        };
    },
    methods: {
        openPreview() {
            this.isOpen = true;
            try {
                this.originalBodyOverflow = document.body.style.overflow;
                document.body.style.overflow = 'hidden';
                window.addEventListener('keydown', this.handleKeydown);
            }
            catch (e) {}
        },
        closePreview() {
            this.isOpen = false;
            try {
                document.body.style.overflow = this.originalBodyOverflow || '';
                window.removeEventListener('keydown', this.handleKeydown);
            }
            catch (e) {}
        },
        handleKeydown(event) {
            const key = event && (event.key || event.keyCode);
            if (key === 'Escape' || key === 'Esc' || key === 27) {
                this.closePreview();
            }
        },
        handleFullImageLoaded() {
            // reserved for future enhancements (e.g., loading indicator)
        }
    },
    beforeDestroy() {
        try {
            window.removeEventListener('keydown', this.handleKeydown);
            document.body.style.overflow = this.originalBodyOverflow || '';
        }
        catch (e) {}
    }
};
</script>

<style lang="stylus" scoped>
.image-preview
    display inline-block
    cursor zoom-in

.image-preview-thumb
    display block
    max-width 100%
    height auto
    border-radius 4px

.fade-zoom-enter-active,
.fade-zoom-leave-active
    transition opacity .25s ease

.fade-zoom-enter,
.fade-zoom-leave-to
    opacity 0

.image-preview-overlay
    position fixed
    top 0
    right 0
    bottom 0
    left 0
    background rgba(0,0,0,0.85)
    display flex
    align-items center
    justify-content center
    z-index 10000
    cursor zoom-out

.image-preview-full
    max-width 92vw
    max-height 92vh
    object-fit contain
    box-shadow 0 12px 40px rgba(0,0,0,0.45)
    border-radius 6px

.image-preview-close
    position absolute
    top 12px
    right 12px
    width 36px
    height 36px
    border none
    border-radius 36px
    background rgba(255,255,255,0.15)
    color #fff
    font-size 26px
    line-height 36px
    text-align center
    cursor pointer
    outline none

    &:hover
        background rgba(255,255,255,0.25)
</style>
