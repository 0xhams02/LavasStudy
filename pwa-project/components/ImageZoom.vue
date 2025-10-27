<template>
    <div class="image-zoom">
        <img
            class="image-zoom__thumb"
            :src="src"
            :alt="alt"
            @click="open"
        >

        <transition name="image-zoom-fade">
            <div
                class="image-zoom__overlay"
                v-if="isOpen"
                @click.self="close"
            >
                <img
                    ref="full"
                    class="image-zoom__full"
                    :class="{ 'is-draggable': scale > 1, 'is-dragging': isDragging }"
                    :src="src"
                    :alt="alt"
                    :style="fullImageStyle"
                    @mousedown="onMouseDown"
                    @dblclick.prevent="toggleScale"
                    @wheel.prevent="handleWheel"
                    @touchstart.passive="onTouchStart"
                    @touchmove.prevent="onTouchMove"
                    @touchend="onTouchEnd"
                >

                <div class="image-zoom__controls">
                    <button class="image-zoom__btn" @click.stop="zoomOut" :disabled="scale <= minScale">-</button>
                    <button class="image-zoom__btn" @click.stop="zoomIn" :disabled="scale >= maxScale">+</button>
                    <button class="image-zoom__btn image-zoom__btn--close" @click.stop="close">×</button>
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
export default {
    name: 'ImageZoom',
    props: {
        src: {
            type: String,
            required: true
        },
        alt: {
            type: String,
            default: ''
        },
        maxScale: {
            type: Number,
            default: 3
        },
        minScale: {
            type: Number,
            default: 1
        },
        step: {
            type: Number,
            default: 0.5
        }
    },
    data() {
        return {
            isOpen: false,
            scale: 1,
            translateX: 0,
            translateY: 0,
            isDragging: false,
            dragStartX: 0,
            dragStartY: 0,
            startTranslateX: 0,
            startTranslateY: 0,
            lastTapTs: 0,
            touchDragging: false
        };
    },
    computed: {
        fullImageStyle() {
            const transform = `translate(${this.translateX}px, ${this.translateY}px) scale(${this.scale})`;
            return {
                transform,
                WebkitTransform: transform
            };
        }
    },
    methods: {
        open() {
            this.isOpen = true;
            this.scale = 1;
            this.translateX = 0;
            this.translateY = 0;
            window.addEventListener('mousemove', this.onMouseMove);
            window.addEventListener('mouseup', this.onMouseUp);
        },
        close() {
            this.isOpen = false;
            this.isDragging = false;
            window.removeEventListener('mousemove', this.onMouseMove);
            window.removeEventListener('mouseup', this.onMouseUp);
        },
        zoomIn() {
            this.scale = Math.min(this.maxScale, +(this.scale + this.step).toFixed(2));
        },
        zoomOut() {
            this.scale = Math.max(this.minScale, +(this.scale - this.step).toFixed(2));
            if (this.scale === 1) {
                this.translateX = 0;
                this.translateY = 0;
            }
        },
        toggleScale() {
            if (this.scale === 1) {
                this.scale = Math.min(2, this.maxScale);
            } else {
                this.scale = 1;
                this.translateX = 0;
                this.translateY = 0;
            }
        },
        handleWheel(event) {
            const delta = event.deltaY > 0 ? -this.step : this.step;
            const next = Math.max(this.minScale, Math.min(this.maxScale, +(this.scale + delta).toFixed(2)));
            this.scale = next;
            if (this.scale === 1) {
                this.translateX = 0;
                this.translateY = 0;
            }
        },
        onMouseDown(event) {
            if (this.scale <= 1) {
                return;
            }
            this.isDragging = true;
            this.dragStartX = event.clientX;
            this.dragStartY = event.clientY;
            this.startTranslateX = this.translateX;
            this.startTranslateY = this.translateY;
        },
        onMouseMove(event) {
            if (!this.isDragging) {
                return;
            }
            const dx = event.clientX - this.dragStartX;
            const dy = event.clientY - this.dragStartY;
            this.translateX = this.startTranslateX + dx;
            this.translateY = this.startTranslateY + dy;
        },
        onMouseUp() {
            this.isDragging = false;
        },
        onTouchStart(event) {
            if (event.touches.length === 1) {
                // single finger drag start or double-tap detection
                const now = Date.now();
                if (now - this.lastTapTs < 300) {
                    this.toggleScale();
                    this.lastTapTs = 0;
                } else {
                    this.lastTapTs = now;
                }
                if (this.scale > 1) {
                    const t = event.touches[0];
                    this.touchDragging = true;
                    this.dragStartX = t.clientX;
                    this.dragStartY = t.clientY;
                    this.startTranslateX = this.translateX;
                    this.startTranslateY = this.translateY;
                }
            }
        },
        onTouchMove(event) {
            if (!this.touchDragging || this.scale <= 1) {
                return;
            }
            const t = event.touches[0];
            const dx = t.clientX - this.dragStartX;
            const dy = t.clientY - this.dragStartY;
            this.translateX = this.startTranslateX + dx;
            this.translateY = this.startTranslateY + dy;
        },
        onTouchEnd() {
            this.touchDragging = false;
        }
    }
};
</script>

<style lang="stylus" scoped>
.image-zoom
    display inline-block

.image-zoom__thumb
    display block
    max-width 100%
    cursor zoom-in

.image-zoom-fade-enter-active,
.image-zoom-fade-leave-active
    transition opacity .2s ease

.image-zoom-fade-enter,
.image-zoom-fade-leave-to
    opacity 0

.image-zoom__overlay
    position fixed
    left 0
    top 0
    right 0
    bottom 0
    background rgba(0, 0, 0, .85)
    z-index 9999
    display flex
    justify-content center
    align-items center
    overflow hidden

.image-zoom__full
    max-width 90vw
    max-height 90vh
    user-select none
    -webkit-user-drag none
    will-change transform
    transition transform .05s linear
    cursor zoom-out

    &.is-draggable
        cursor grab

    &.is-dragging
        cursor grabbing

.image-zoom__controls
    position fixed
    right 16px
    top 16px
    display flex
    gap 8px

.image-zoom__btn
    min-width 32px
    height 32px
    line-height 30px
    text-align center
    padding 0 8px
    border none
    border-radius 16px
    background rgba(255, 255, 255, .2)
    color #fff
    font-size 18px

    &:disabled
        opacity .4

.image-zoom__btn--close
    font-size 22px
    font-weight 700
</style>
