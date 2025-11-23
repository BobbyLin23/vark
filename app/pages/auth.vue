<script lang="ts" setup>
const state = ref<'login' | 'register'>('login')

function toggleState(value: 'login' | 'register') {
  state.value = value
}
</script>

<template>
  <div class="flex min-h-svh w-full items-center justify-center p-6 md:p-10">
    <div class="w-full max-w-sm">
      <AuthLoginCard v-if="state === 'login'" @toggle-state="toggleState" />
      <AuthRegisterCard v-else @toggle-state="toggleState" />
    </div>
  </div>
</template>
