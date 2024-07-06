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
  clearInterval(interval)

  if (regenerate.value) {
    interval = setInterval(() => (seed.value = Math.random()), regenRate.value)
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
      <symbol id="digit" viewBox="0 0 40 59" fill="currentColor">
        <polygon
          style="fill: var(--bit-0)"
          points="12.64 25.98 28.61 25.98 27.49 32.34 11.53 32.34 12.64 25.98"
        />
        <polygon
          style="fill: var(--bit-1)"
          points="4.11 26.94 7.37 7.12 11.81 1.05 14.11 8.56 10.86 28.41 4.11 26.94"
        />
        <polygon
          style="fill: var(--bit-2)"
          points="10.57 29.73 6.89 50.73 2.3 57.76 0 50.73 3.82 28.25 10.57 29.73"
        />
        <polygon
          style="fill: var(--bit-3)"
          points="7.94 51.4 24.94 51.4 27.24 59 3.23 59 7.94 51.4"
        />
        <polygon
          style="fill: var(--bit-4)"
          points="35.82 32.95 32.62 51.69 28.19 57.76 25.89 50.24 29.07 31.48 35.82 32.95"
        />
        <polygon
          style="fill: var(--bit-5)"
          points="29.36 30.16 33.06 8.4 37.7 1.05 40 8.08 36.11 31.64 29.36 30.16"
        />
        <polygon
          style="fill: var(--bit-6)"
          points="32.11 7.6 15.12 7.6 12.82 0 36.83 0 32.11 7.6"
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
