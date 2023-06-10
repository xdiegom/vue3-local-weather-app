<template>
    <Suspense>
        <AsyncCityView />

        <template #fallback>
            <p>Loading...</p>
        </template>
    </Suspense>
</template>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue';


</script>
