<template>
  <header>
    <div class="navbar bg-base-100">
      <div class="flex-1">
        <NuxtLink class="btn btn-ghost text-xl" to="/"
          ><span class="material-symbols-outlined">
            format_list_numbered_rtl
          </span>
          BoTool</NuxtLink
        >
      </div>
      <div class="flex-none">
        <ul class="menu menu-horizontal px-1">
          <li><NuxtLink to="/build-orders">Build Orders</NuxtLink></li>
        </ul>
      </div>
    </div>
  </header>
  <main>
    <slot />
  </main>
</template>
