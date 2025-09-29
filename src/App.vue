<template>
  <div class="p-6 space-y-6">
    <h1 class="text-2xl font-bold">VueUse Demo</h1>
    <NetworkStatus />
    <WebNotification />
  </div>
</template>

<script setup lang="ts">
import NetworkStatus from './components/NetworkStatus.vue'
import WebNotification from './components/WebNotification.vue'
</script>
