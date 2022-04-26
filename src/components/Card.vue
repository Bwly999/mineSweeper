<script lang="ts" setup>
interface CardProps {
  name: string
//   y: number
}
defineProps<CardProps>()
</script>

<template>
  <div flex flex-col w-60vw aspect="3/2" bg-white rounded-xl>
    <div justify-self-start self-start w="100%" h="40%" bg-blue text-red rounded-t-xl border-0 flex justify-center>
      <img m1 src="/maw.png">
    </div>
    <div text-black font-bold justify-end justify-items-end flex-1 flex items-center justify-center>
      <div>
        Card {{ name }}
      </div>
    </div>
  </div>
</template>
