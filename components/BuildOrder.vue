<script setup lang="ts">
import { nextTick, ref, toRefs } from 'vue';

const props = defineProps({
  buildOrderData: Array<{
    id: string;
    time: number;
    at: string;
    instruction: Array<string>;
  }>,
});

const running = ref(false);
const time = ref(1);

const buildStepRefs = ref<Array<HTMLDivElement>>([]);

if (props.buildOrderData === undefined) {
  throw new Error('No build order was passed as prop!');
}

const buildOrder = ref(
  props.buildOrderData.map((item) => ({
    ...item,
    active: false,
  })),
);
const interval = ref<NodeJS.Timeout>();

function startStop() {
  clearInterval(interval.value);
  running.value = !running;

  if (!running) {
    return;
  }

  interval.value = setInterval(() => {
    if (!buildOrder) {
      return;
    }

    buildOrder.value.map(async (item, index) => {
      if (time.value === item.time) {
        const tmp = buildOrder.value;
        if (!tmp) {
          return;
        }
        if (index !== 0) {
          tmp[index - 1].active = false;
        }

        tmp[index].active = true;
        buildOrder.value = tmp;
        await nextTick();

        if (index !== 0) {
          buildStepRefs.value[index - 1].scrollIntoView();
        } else {
          buildStepRefs.value[index].scrollIntoView();
        }
      }
    });

    const lastItem = buildOrder.value.slice(-1)[0];
    if (time.value === lastItem.time) {
      clearInterval(interval.value);
      running.value = false;
      time.value = 1;
      return;
    }

    time.value = time.value + 1;
  }, 1000);
}

function reset() {
  clearInterval(interval.value);
  time.value = 1;
  window.scrollTo(0, 0);
  if (!buildOrder) {
    return;
  }
  buildOrder.value.map((item, index) => {
    if (!buildOrder) {
      return;
    }
    item.active && (buildOrder.value[index].active = false);
  });
}
</script>

<template>
  <div class="max-w-md mx-auto">
    <div
      class="p-3 absolute top-2 right-2 h-12 bg-sky-950 border-white border-2"
    >
      {{ time }}
    </div>
    <div
      v-for="(buildStep, index) in buildOrder"
      :key="index"
      class="grid grid-cols-2 items-center m-2 p-3 bg-purple-500 font-bold"
      :class="{ 'border-2 border-white text-4xl py-12': buildStep.active }"
      ref="buildStepRefs"
    >
      <div>{{ buildStep.at }}</div>
      <ul class="list-disc">
        <li v-for="(instruction, index) in buildStep.instruction" :key="index">
          {{ instruction }}
        </li>
      </ul>
    </div>
    <div class="sticky bottom-0 flex bg-white m-2">
      <button
        class="m-1 p-3 bg-purple-500 w-full h-24 border-2 border-white"
        @click="startStop"
      >
        {{ running ? 'Stop' : 'Start' }}
      </button>
      <button
        class="m-1 p-3 bg-purple-500 w-full h-24 border-2 border-white"
        @click="reset"
      >
        Reset
      </button>
    </div>
  </div>
</template>
