<template>
  <div class="slidev-layout bilingual">
    <div class="en-zone">
      <slot />
    </div>
    <div class="zh-zone">
      <slot name="zh" />
    </div>
  </div>
</template>

<style>
.bilingual {
  display: flex;
  flex-direction: column;
  height: 100%;
  background: #ffffff;
  padding: 2.5rem 5rem;
}

.bilingual .en-zone {
  flex: 3;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
}

.bilingual .en-zone p,
.bilingual .en-zone h1,
.bilingual .en-zone h2,
.bilingual .en-zone h3 {
  font-size: 3.2rem;
  font-weight: 300;
  color: #464462;
  line-height: 1.4;
  margin: 0;
}

.bilingual .en-zone code {
  font-family: 'Fira Code', monospace;
  font-size: 2.8rem;
  background: transparent;
  color: #464462;
}

.bilingual .zh-zone {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  border-top: 1px solid #e8e8ee;
}

.bilingual .zh-zone p {
  font-size: 1.2rem;
  font-weight: 300;
  color: #7B7B7B;
  line-height: 1.5;
  margin: 0;
}

.bilingual .zh-zone code {
  font-family: 'Fira Code', monospace;
  font-size: 1.1rem;
  background: transparent;
  color: #7B7B7B;
}
</style>
