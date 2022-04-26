<script lang="ts" setup>
// defineProps<{  }>()
const time = ref(0)
let timer = setInterval(() => {
  time.value++
}, 1000)

function stop() {
  clearInterval(timer)
}
function reset() {
  time.value = 0
  clearInterval(timer)
  timer = setInterval(() => {
    time.value++
  }, 1000)
}
defineExpose({
  stop,
  reset,
})

</script>
<template>
  <div flex justify-center font-bold text-2xl>
    <p mr-5>
      Timer:
    </p>
    <div text-red-500>
      {{ time }}
    </div>
  </div>
</template>
