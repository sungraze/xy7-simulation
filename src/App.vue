<script setup>
import { sampleSize } from 'lodash'
import { ref, computed, watchEffect } from 'vue'

import Panel from '/src/components/Panel.vue'

const totalPanels = ref(8)
const panelColumns = ref(4)
const digitSize = ref(0.5)
const regenerate = ref(false)
const regenRate = ref(200)

const possibleNumbers = [...Array(128).keys()]

const seed = ref(Math.random())

let panelData = computed(() => {
  // hacky way to force recompute, seed not actually used
  seed.value

  return [...Array(totalPanels.value).keys()].map(() =>
    sampleSize(possibleNumbers, 28)
  )
})

let interval = null

watchEffect(() => {
  if (regenerate.value) {
    // in case regenRate changes
    clearInterval(interval)

    interval = setInterval(() => {
      seed.value = Math.random()
    }, regenRate.value)
  } else {
    clearInterval(interval)
  }
})
</script>

<template>
  <main class="p-10">
    <div class="mb-10 flex items-center">
      <label class="flex items-center">
        <span class="pr-4">Number of panels</span>
        <input type="number" v-model="totalPanels" class="w-24" />
      </label>

      <label class="flex items-center ml-10">
        <span class="pr-4">Panels per row</span>
        <input type="number" v-model="panelColumns" class="w-24" />
      </label>

      <label class="flex items-center ml-10">
        <span class="pr-4">Relative digit size</span>

        <select v-model.number="digitSize" class="w-24">
          <option value="0.25">25%</option>
          <option value="0.5">50%</option>
          <option value="0.75">75%</option>
          <option value="1">100%</option>
        </select>
      </label>
    </div>
    <p class="font-bold mb-4">
      Data is a random sample of all the possible values (0-127)
    </p>

    <div class="mb-10 flex items-center">
      <label class="flex items-center cursor-pointer">
        <input type="checkbox" v-model="regenerate" />
        <span class="ml-2">Continously regenerate</span>
      </label>

      <label class="flex items-center ml-10">
        <span class="pr-4">Rate (ms)</span>
        <input type="number" v-model="regenRate" class="w-24" min="16" />
      </label>
    </div>

    <div class="housing">
      <Panel
        v-for="panelIndex in totalPanels"
        :key="panelIndex"
        :data="panelData[panelIndex - 1]"
        :digitSize="digitSize"
      />
    </div>

    <p class="mt-10">
      <a href="https://github.com/sungraze/xy7-simulation">Github</a>
    </p>
  </main>

  <svg>
    <defs>
      <symbol id="digit" viewBox="0 0 100 147.49" fill="currentColor">
        <polygon
          style="fill: var(--bit-0)"
          points="31.59 64.95 71.52 64.95 68.72 80.86 28.82 80.86 31.59 64.95"
        />
        <polygon
          style="fill: var(--bit-1)"
          points="10.28 67.35 18.43 17.8 29.52 2.63 35.27 21.41 27.16 71.03 10.28 67.35"
        />
        <polygon
          style="fill: var(--bit-2)"
          points="26.43 74.32 17.22 126.82 5.74 144.4 0 126.82 9.56 70.63 26.43 74.32"
        />
        <polygon
          style="fill: var(--bit-3)"
          points="19.86 128.5 62.34 128.5 68.09 147.49 8.07 147.49 19.86 128.5"
        />
        <polygon
          style="fill: var(--bit-4)"
          points="89.55 82.38 81.56 129.23 70.48 144.4 64.73 125.61 72.68 78.7 89.55 82.38"
        />
        <polygon
          style="fill: var(--bit-5)"
          points="73.4 75.41 82.64 21.01 94.26 2.63 100 20.21 90.28 79.1 73.4 75.41"
        />
        <polygon
          style="fill: var(--bit-6)"
          points="80.28 18.99 37.8 18.99 32.04 0 92.07 0 80.28 18.99"
        />
      </symbol>
    </defs>
  </svg>
</template>

<style>
body {
  background: theme(colors.gray.950);
  color: theme(colors.gray.100);

  input,
  select {
    color: theme(colors.gray.900);
  }

  a {
    text-decoration: underline;
  }
}

:root {
  --bit-off: theme(colors.gray.900);
  --bit-on: theme(colors.gray.50);
}

.housing {
  display: inline-grid;
  grid-template-columns: repeat(
    v-bind('panelColumns'),
    minmax(min-content, 1fr)
  );
}
</style>
