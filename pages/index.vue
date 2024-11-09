<script setup lang="ts">
import BuildOrder from './../components/BuildOrder.vue';

const { $supabase } = useNuxtApp();

const loading = useLoadingIndicator();

type BuildOrderType = {
  id: string;
  time: number;
  at: string;
  instruction: Array<string>;
};

const { data: buildOrderSteps } = await useAsyncData<BuildOrderType[]>(
  'buildOrderSteps',
  async () => {
    const { data, error } = await $supabase
      .from('build_order_step')
      .select('*');
    if (error) throw new Error('No build order steps found!');
    return data;
  },
);
</script>

<template>
  <div class="h-dvh overflow-hidden">
    <BuildOrder :buildOrderData="buildOrderSteps ?? []"></BuildOrder>
  </div>
</template>
