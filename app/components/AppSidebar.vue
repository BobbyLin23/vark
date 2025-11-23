<script setup lang="ts">
import type { SidebarProps } from '@/components/ui/sidebar'

const props = withDefaults(defineProps<SidebarProps>(), {
  collapsible: 'icon',
})
</script>

<template>
  <Sidebar v-bind="props">
    <SidebarFooter>
      <UserButton />
    </SidebarFooter>
    <SidebarRail />
  </Sidebar>
</template>
