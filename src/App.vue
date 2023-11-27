<script setup lang="ts">
const buttons = [
  { type: "action", value: "AC", id: "clear" },
  { type: "action", value: "&lArr;", id: "back" },
  { type: "action", value: "%", id: "percent" },
  { type: "action", value: "/", id: "divide" },
  { type: "number", value: "7", id: "seven" },
  { type: "number", value: "8", id: "eight" },
  { type: "number", value: "9", id: "nine" },
  { type: "action", value: "x", id: "multiply" },
  { type: "number", value: "4", id: "four" },
  { type: "number", value: "5", id: "five" },
  { type: "number", value: "6", id: "six" },
  { type: "action", value: "-", id: "subtract" },
  { type: "number", value: "1", id: "one" },
  { type: "number", value: "2", id: "two" },
  { type: "number", value: "3", id: "three" },
  { type: "action", value: "+", id: "add" },
  { type: "number", value: "0", id: "zero" },
  { type: "decimal", value: ".", id: "decimal" },
  { type: "action", value: "=", id: "equals" },
];
</script>

<template>
  <div
    id="calculator"
    class="w-72 shrink-0 rounded-lg border-2 bg-black text-lg text-white"
  >
    <div
      id="screen"
      class="flex min-h-[126px] flex-col items-end justify-end bg-gray-500 p-2"
    >
      <div id="formula" class="">2+3</div>
      <div id="display">5</div>
    </div>
    <div class="buttons grid grid-cols-4 gap-4 p-2">
      <button
        v-for="b in buttons"
        :key="b.value"
        :id="b.id"
        class="h-12 w-12 rounded-full"
      >
        <p
          class="h-12 w-12 rounded-full bg-slate-300 bg-opacity-0 leading-[3rem] transition-colors duration-500 hover:bg-opacity-20"
          v-html="b.value"
        />
      </button>
    </div>
  </div>
</template>

<style scoped>
/* 
break-normal	overflow-wrap: normal; word-break: normal;
break-words	overflow-wrap: break-word;
break-all	word-break: break-all;
break-keep	word-break: keep-all;
*/
#formula {
  @apply break-all text-right;
}

#zero {
  @apply col-start-2;
}

#divide,
#multiply,
#subtract,
#add {
  @apply bg-gray-800;
}

#equals {
  @apply bg-red-500;
}
</style>
