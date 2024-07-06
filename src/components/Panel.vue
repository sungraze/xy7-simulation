<script setup>
const { data, digitSize } = defineProps({ data: Array, digitSize: Number })

// Ex: 1000011 => 'bit-6 bit-1 bit-0'
const bitClasses = (num) => {
  return num
    .toString(2)
    .split('')
    .reverse()
    .reduce(
      (acc, bit, index) => (bit === '1' ? `${acc} bit-${index}` : acc),
      ''
    )
}
</script>

<template>
  <div class="panel">
    <svg v-for="i in data" :key="i" class="digit" :class="bitClasses(i)">
      <use href="#digit" />
    </svg>
  </div>
</template>

<style>
.panel {
  display: grid;
  grid-template-columns: repeat(7, minmax(min-content, 1fr));
  grid-gap: 1px;
}

.digit {
  display: block;

  --digit-width: 40px * v-bind('digitSize');
  --digit-height: 59px * v-bind('digitSize');
  --digit-px: calc(var(--digit-width) * 0.2);
  --digit-py: calc(var(--digit-height) * 0.26);

  width: calc(var(--digit-width) + var(--digit-px) * 2);
  height: calc(var(--digit-height) + var(--digit-py) * 2);
  padding: var(--digit-py) var(--digit-px);

  color: var(--bit-off);

  outline: 1px solid var(--bit-off);
}

.bit-0 {
  --bit-0: var(--bit-on);
}

.bit-1 {
  --bit-1: var(--bit-on);
}

.bit-2 {
  --bit-2: var(--bit-on);
}

.bit-3 {
  --bit-3: var(--bit-on);
}

.bit-4 {
  --bit-4: var(--bit-on);
}

.bit-5 {
  --bit-5: var(--bit-on);
}

.bit-6 {
  --bit-6: var(--bit-on);
}
</style>
