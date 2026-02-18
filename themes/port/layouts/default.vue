<template>
  <div class="slidev-layout default h-full flex flex-col px-12 py-10">
    <slot />
  </div>
</template>

<script setup lang="ts">
import { onMounted } from 'vue'

function constrainMermaidSvgs() {
  let needsRetry = false
  document.querySelectorAll<HTMLElement>('.mermaid:not([data-constrained])').forEach(el => {
    const svg = el.shadowRoot?.querySelector<SVGElement>('svg')
    if (!svg) {
      needsRetry = true
      return
    }
    svg.style.maxHeight = '30vh'
    svg.style.width = 'auto'
    el.dataset.constrained = '1'
  })

  if (needsRetry) {
    setTimeout(constrainMermaidSvgs, 100)
  }
}

onMounted(() => {
  if ((window as any).__mermaidConstrained) {
    return
  }
  ;(window as any).__mermaidConstrained = true

  document.fonts.ready.then(() => {
    constrainMermaidSvgs()

    const observer = new MutationObserver(mutations => {
      const hasMermaid = mutations.some(m =>
        Array.from(m.addedNodes).some(n =>
          n instanceof Element &&
          (n.classList.contains('mermaid') || n.querySelector('.mermaid'))
        )
      )
      if (hasMermaid) {
        constrainMermaidSvgs()
      }
    })
    observer.observe(document.body, { childList: true, subtree: true })
  })
})
</script>
