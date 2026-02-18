<script setup lang="ts">
defineProps<{
  gap?: 'small' | 'medium' | 'large'
  inline?: boolean
}>()
</script>

<template>
  <div :class="['stack', gap || 'medium', inline && 'inline']">
    <slot />
  </div>
</template>

<style scoped>
.stack {
  display: flex;
  flex-direction: column;
}

.stack.inline {
  align-items: flex-start;
}

.stack.small {
  gap: 0.5rem;
}

.stack.medium {
  gap: 1rem;
}

.stack.large {
  gap: 1.5rem;
}
</style>
