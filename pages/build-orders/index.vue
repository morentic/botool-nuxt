<script setup lang="ts">
const { $supabase } = useNuxtApp();

const { data: buildOrders } = await useAsyncData('buildOrders', async () => {
  const { data, error } = await $supabase.from('build_order').select('*');
  if (error) throw new Error('No build orders found!');
  return data;
});
</script>

<template>
  <div class="overflow-x-auto p-3">
    <h1 class="text-4xl mb-3">Build Orders</h1>
    <table class="table">
      <!-- head -->
      <thead>
        <tr>
          <th>
            <label>
              <input type="checkbox" class="checkbox" />
            </label>
          </th>
          <th>Name</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="buildOrder in buildOrders">
          <th>
            <label>
              <input type="checkbox" class="checkbox" />
            </label>
          </th>
          <td>
            {{ buildOrder.name }}
          </td>
          <th>
            <NuxtLink
              :to="'/build-orders/' + buildOrder.id"
              class="link link-primary"
            >
              Details
            </NuxtLink>
          </th>
        </tr>
      </tbody>
      <!-- foot -->
      <tfoot>
        <tr>
          <th></th>
          <th>Name</th>
          <th></th>
        </tr>
      </tfoot>
    </table>
  </div>
  <ul>
    <li v-for="buildOrder in buildOrders"></li>
  </ul>
</template>
