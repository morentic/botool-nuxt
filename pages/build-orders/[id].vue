<script setup lang="ts">
import BuildOrder from './../../components/BuildOrder.vue';

definePageMeta({
  layout: false,
});

const { $supabase } = useNuxtApp();
const route = useRoute();

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
      .select('*')
      .eq('build_order_id', route.params.id);
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
