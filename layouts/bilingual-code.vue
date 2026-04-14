<template>
  <div class="slidev-layout bilingual-code">
    <div class="code-zone">
      <slot />
    </div>
    <div class="zh-zone">
      <slot name="zh" />
    </div>
  </div>
</template>

<style>
.bilingual-code {
  display: flex;
  flex-direction: column;
  height: 100%;
  background: #ffffff;
  padding: 2.5rem 5rem;
}

.bilingual-code .code-zone {
  flex: 3;
  display: flex;
  align-items: center;
  justify-content: center;
}

.bilingual-code .code-zone div.slidev-code,
.bilingual-code .code-zone pre,
.bilingual-code .code-zone .shiki {
  background-color: #1e1c2e !important;
  border-radius: 10px;
  padding: 1.8rem 2.5rem !important;
  font-size: 1rem;
  line-height: 1.7;
  width: 100%;
  max-width: 860px;
  text-align: left;
}

/* Force dark-theme token colors on dark background */
.bilingual-code .code-zone .shiki span {
  color: var(--shiki-dark) !important;
}

.bilingual-code .code-zone .shiki {
  color: var(--shiki-dark) !important;
}

.bilingual-code .zh-zone {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  border-top: 1px solid #e8e8ee;
}

.bilingual-code .zh-zone p {
  font-size: 1.2rem;
  font-weight: 300;
  color: #7B7B7B;
  line-height: 1.5;
  margin: 0;
}

.bilingual-code .zh-zone code {
  font-family: 'Fira Code', monospace;
  font-size: 1.1rem;
  background: transparent;
  color: #7B7B7B;
}
</style>
