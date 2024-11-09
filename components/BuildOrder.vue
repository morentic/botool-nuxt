<script setup lang="ts">
import { nextTick, ref, toRefs } from 'vue';

const router = useRouter();

const props = defineProps({
  buildOrderData: Array<{
    id: string;
    time: number;
    at: string;
    instruction: Array<string>;
  }>,
});

const running = ref<boolean>(false);
const time = ref(1);

const buildStepRefs = ref<Array<HTMLDivElement>>([]);

if (props.buildOrderData === undefined) {
  throw new Error('No build order was passed as prop!');
}

const buildOrder = ref(
  props.buildOrderData.map((item) => ({
    ...item,
    active: false,
    done: false,
  })),
);
const interval = ref<NodeJS.Timeout>();

function startStop() {
  clearInterval(interval.value);
  running.value = !running.value;

  if (!running.value) {
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
          tmp[index - 1].done = true;
        }

        tmp[index].active = true;
        buildOrder.value = tmp;
        await nextTick();

        if (index !== 0) {
          buildStepRefs.value[index - 1].scrollIntoView({ behavior: 'smooth' });
        } else {
          buildStepRefs.value[index].scrollIntoView({ behavior: 'smooth' });
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
  document
    .querySelectorAll('.timeline-vertical > li')[0]
    .scrollIntoView({ behavior: 'smooth' });
  if (!buildOrder) {
    return;
  }
  buildOrder.value.map((item, index) => {
    if (!buildOrder) {
      return;
    }
    item.active && (buildOrder.value[index].active = false);
    item.done = false;
  });
}
</script>

<template>
  <div class="max-w-md mx-auto">
    <div class="absolute top-2 left-2 z-10">
      <a @click="router.back()" class="link link-neutral">Back</a>
    </div>
    <div class="absolute top-2 right-2">
      <span class="badge badge-primary">{{ time }}</span>
    </div>
    <ul class="timeline timeline-vertical">
      <li
        v-for="(buildStep, index) in buildOrder"
        :key="index"
        ref="buildStepRefs"
      >
        <hr v-if="index !== 0" />
        <div
          class="timeline-start text-lg"
          :class="{ 'text-accent': buildStep.active }"
        >
          {{ buildStep.at }}
        </div>
        <div class="timeline-middle">
          <span
            class="material-symbols-outlined"
            :class="{ 'text-accent': buildStep.active }"
            >{{
              buildStep.done ? 'check_circle' : 'radio_button_unchecked'
            }}</span
          >
        </div>
        <div
          class="timeline-end timeline-box p-5"
          :class="[
            buildStep.active ? 'border-accent font-bold' : 'border-neutral',
          ]"
        >
          <ul class="list-none text-lg">
            <li
              v-for="(instruction, index) in buildStep.instruction"
              :key="index"
            >
              {{ instruction }}
            </li>
          </ul>
        </div>
        <hr />
      </li>
    </ul>
    <div class="sticky bottom-2 grid grid-cols-2 gap-2 m-2">
      <button class="btn btn-neutral btn-block" @click="reset">Reset</button>
      <button class="btn btn-primary btn-block" @click="startStop">
        {{ running ? 'Stop' : 'Start' }}
      </button>
    </div>
  </div>
</template>
