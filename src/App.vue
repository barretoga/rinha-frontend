<script setup lang="ts">
import { ref, watch } from 'vue'
import VirtualScroller from '~/components/VirtualScroller.vue'

const myMassiveArray = ref()

function handleJsonFile(file: any) {
  const reader = new FileReader()
  reader.onload = (e) => {
    if (e?.target?.result) {
      const json = JSON.parse(e.target.result as string)
      const stringify = JSON.stringify(json)

      const size = 80
      const numChunks = Math.ceil(stringify.length / size)
      const chunks = new Array(numChunks)

      for (let i = 0, o = 0; i < numChunks; ++i, o += size) {
        chunks[i] = stringify.substr(o, size)
      }

      myMassiveArray.value = chunks
    }
  }
  reader.readAsText(file)
}

watch(
  myMassiveArray,
  (newVal) => {
    console.log(newVal)
  }
)

</script>

<template>
  <main>
    <h1>JSON Tree Viewer</h1>

    <div class="input-box">
      <label for="jsonfile">
        Selecione o arquivo JSON:
      </label>
      <input name="jsonfile" type="file" @change="(e: any) => handleJsonFile(e.target.files[0])" />
    </div>

    <VirtualScroller v-if="myMassiveArray" v-slot="{ item }" :item-height="35" :items="myMassiveArray">
      <div :item="item" :key="item">
        {{ item }}
      </div>
    </VirtualScroller>
  </main>
</template>

<style>
.input-box {
  display: flex;
  flex-direction: column;
  margin: 10px 0px 0px 0px;
}
</style>
