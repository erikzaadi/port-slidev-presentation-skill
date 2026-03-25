<template>
  <div class="disclaimer">
    <span class="disclaimer-label">Disclaimer:</span> <slot />
  </div>
</template>

<style scoped>
.disclaimer {
  position: absolute;
  bottom: 1.5rem;
  left: 0;
  right: 0;
  text-align: center;
  font-size: 0.6rem;
  opacity: 0.5;
}

.disclaimer-label {
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}
</style>
