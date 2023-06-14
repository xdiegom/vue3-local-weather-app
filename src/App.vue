<template>
  <div class="flex flex-col min-h-screen font-roboto bg-weather-primary">
    <SiteNavigation />
    <RouterView v-slot="{ Component, route }">
      <Transition name="page" mode="out-in">
        <div :key="route.name">  
          <component :is="Component" />
        </div>
      </Transition>
    </RouterView>
  </div>
</template>


<script setup>
import SiteNavigation from "./components/SiteNavigation.vue"
import { RouterView } from 'vue-router';
</script>


<style>
.page-enter-active,
.page-leave-active {
  transition: 300ms ease all;
}

/** 
  By default, if "page-enter-to" and "page-leave-from" are not present, when the transition
  state is "active", if opacity or other css attribute is not defined (as far as I understood)
  the opacity is set to 1. That's why it is not use here

*/

.page-enter-from,
.page-leave-to {
  opacity: 0;
}
</style>
