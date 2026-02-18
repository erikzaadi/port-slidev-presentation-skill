<template>
  <div class="slidev-layout wide-image h-full grid">
    <div class="content">
      <slot />
    </div>
    <div class="image">
      <slot name="right" />
    </div>
  </div>
</template>

<style scoped>
.slidev-layout.wide-image {
  grid-template-columns: 40% 60%;
  padding: 0;
}

.content {
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 2.5rem 2rem 2.5rem 3rem;
}

.image {
  overflow: hidden;
}

.image :deep(img) {
  width: 100%;
  height: 100%;
  object-fit: cover;
}
</style>
