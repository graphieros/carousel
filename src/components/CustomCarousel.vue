<script setup lang="ts">

    import { ref, computed, onMounted, onUnmounted } from 'vue';

    const props = defineProps({
        background: {
            type: String,
            default: "transparent",
        },
        border: {
            type: String,
            default: undefined,
        },
        borderRadius: {
            type: String,
            default: '',
        },
        chevrons: {
            type: Boolean,
            default: true,
        },
        chevronColor: {
            type: String,
            default: "grey",
        },
        chevronSize: {
            type: Number,
            default: 32,
        },
        contentBackground: {
            type: String,
            default: "transparent",
        },
        height: {
            type: String,
            default: "80px",
        },
        htmlIconLeft: {
            type: String,
            default:
                "<path fill='currentColor' d='M15.41,16.58L10.83,12L15.41,7.41L14,6L8,12L14,18L15.41,16.58Z'/>",
        },
        htmlIconRight: {
            type: String,
            default:
                "<path fill='currentColor' d='M8.59,16.58L13.17,12L8.59,7.41L10,6L16,12L10,18L8.59,16.58Z'/>",
        },
        iconViewBox: {
            type: String,
            default: "0 0 24 24",
        },
        items: {
            type: Array,
            default() {
                return [];
            },
        },
        useWidthScroll: {
            type: Boolean,
            default: false,
        },
        scrollStep: {
            type: Number,
            default: 200,
        },
        width: {
            type: String,
            default: "100%",
        },
    });

    const currentScrollPosition = ref(0);
    const isOverflow = ref(false);
    const carousel = ref();

    const chevronStyle = computed(() => {
        return `
            color:${props.chevronColor};
            font-size:${props.chevronSize}px;
            height:${props.chevronSize}px;
            width:${props.chevronSize}px;
        `;
    });

    const computedStyle = computed(() => {
        return `
            background:${props.background};
            border-radius:${props.borderRadius || "none"};
            border:${props.border || "none"};
            height:${props.height};
            width:${props.width};
        `;
    });

    const contentStyle = computed(() => {
        return `
            background:${props.contentBackground};
        `;
    });

    const svgStyle = computed(() => {
        return `
            height:${props.chevronSize};
            width:${props.chevronSize};
        `;
    });

    function scrollTo(direction: string){
        const clientRect = carousel.value.getBoundingClientRect();
        const width = carousel.value.scrollWidth - clientRect.width;
        const scrollStep = props.useWidthScroll
            ? clientRect.width
            : props.scrollStep;

        if (direction === "right") {
            if (currentScrollPosition.value >= width) {
                currentScrollPosition.value = 0;
            } else {
                currentScrollPosition.value += scrollStep;
            }
            carousel.value.scrollTo({
                left: currentScrollPosition.value,
                behavior: "smooth"
            });
        } else {
            if (currentScrollPosition.value <= 0) {
                currentScrollPosition.value = 0;
            } else {
                currentScrollPosition.value -= scrollStep;
            }
            carousel.value.scrollTo({
                left: currentScrollPosition.value,
                behavior: "smooth"
            });
        }
    }

    function toggleOverflow() {
        if(carousel) {
            const clientRect = carousel.value.getBoundingClientRect();
            isOverflow.value = carousel.value.scrollWidth > clientRect.width;
        }
    }

    onMounted(() => {
        carousel.value = document.getElementById("alpCarousel");
        window.addEventListener("resize", toggleOverflow);
        toggleOverflow();
    });
    onUnmounted(() => {
        window.removeEventListener("resize", toggleOverflow);
    });
</script>

<template>
    <div class="carousel-bar" :style="computedStyle">
      <div
        v-if="chevrons && isOverflow"
        tabindex="0"
        class="carousel-bar__chevrons carousel-bar__chevrons--left"
        :style="chevronStyle"
        @click="scrollTo('left')"
        @keypress.enter="scrollTo('left')"
      >
        <svg :style="svgStyle" :viewBox="iconViewBox">
          <g v-html="htmlIconLeft" />
        </svg>
      </div>
      <div class="carousel-bar__content" ref="carouselContent" id="alpCarousel">
        <template v-if="items.length">
          <div v-for="(item, i) in items" :key="`item_${i}`">
            {{ item }}
          </div>
        </template>
        <slot></slot>
      </div>
      <div
        v-if="chevrons && isOverflow"
        tabindex="0"
        class="carousel-bar__chevrons carousel-bar__chevrons--right"
        :style="chevronStyle"
        @click="scrollTo('right')"
        @keypress.enter="scrollTo('right')"
      >
        <svg :style="svgStyle" :viewBox="iconViewBox">
          <g v-html="htmlIconRight" />
        </svg>
      </div>
    </div>
</template>

<style scoped>
    .carousel-bar {
        padding: 12px;
        position: relative;
    }
    .carousel-bar__chevrons {
        align-items: center;
        border-radius: 8px;
        cursor: pointer;
        display: flex;
        justify-content: center;
        position: absolute;
        top: 50%;
        transform: translateY(-50%);
        transition: transform 0.15s ease-in-out;
    }
    .carousel-bar__chevrons:hover {
        transform: translateY(-50%) scale(1.2, 1.2);
    }
    .carousel-bar__chevrons--left {
        left: 12px;
    }
    .carousel-bar__chevrons--right {
        right: 12px;
    }
    .carousel-bar__content {
        align-items: center;
        display: flex;
        gap: 24px;
        height: calc(100% - 24px);
        justify-content: start;
        left: 50%;
        overflow-x: hidden;
        position: absolute;
        transform: translateX(-50%);
        width: calc(100% - 124px);
    }
    .carousel-bar div {
        align-items: center;
        justify-content:center;
        border-radius: 8px;
        color: black;
        display: flex;
        padding: 3 24px;
    }
</style>