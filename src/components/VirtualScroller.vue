<script setup lang="ts">
import { computed, onMounted, ref, toRefs } from 'vue'

interface Props {
  items: any[]
  itemHeight: number
}

const props = defineProps<Props>()
const { items, itemHeight } = toRefs(props)

const scroller = ref<HTMLElement>()
const scrollerHeight = ref(0)
const renderAhead = 5
const scrollTop = ref(0)

const totalHeight = computed(() => items.value.length * itemHeight.value)
const startPosition = computed(() => Math.max(0, Math.floor(scrollTop.value / itemHeight.value) - renderAhead))
const offsetY = computed(() => startPosition.value * itemHeight.value)

const renderedItems = computed(() => {
  let count = Math.ceil(scrollerHeight.value / itemHeight.value) + 2 * renderAhead
  count = Math.min(items.value.length - startPosition.value, count)
  return items.value.slice(startPosition.value, startPosition.value + count)
})

const onScroll = e => requestAnimationFrame(() => scrollTop.value = (e.target as HTMLElement).scrollTop)
const observer = new ResizeObserver(entries => entries.forEach(el => scrollerHeight.value = el.contentRect.height))

onMounted(() => {
  observer.observe(scroller.value!)
  scrollerHeight.value = scroller.value!.offsetHeight
})
</script>

<template>
  <div ref="scroller" class="virtual-scroll" @scroll="onScroll">
    <div :style="{ height: `${totalHeight}px`}">
      <div :style="{ transform: `translateY(${offsetY}px)`}">
        <template v-for="item in renderedItems">
          <slot :item="item" />
        </template>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.virtual-scroller {
  overflow: auto;
  will-change: transform;
  > div {
    overflow: hidden;
    will-change: transform;
    > div {
      will-change: transform;
    }
  }
}
</style>
