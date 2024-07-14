<template>
    <div class="loader"></div>
</template>

<script setup>

</script>

<style scoped>
.loader {
  width: 44px;
  padding: 5px;
  aspect-ratio: 1;
  border-radius: 50%;
  background: #6E7D80;
  --_m:
      conic-gradient(#0000 10%,#6E7D80),
      linear-gradient(#6E7D80 0 0) content-box;
  -webkit-mask: var(--_m);
  mask: var(--_m);
  -webkit-mask-composite: source-out;
  mask-composite: subtract;
  animation: l3 1s infinite linear;
}
@keyframes l3 {to{transform: rotate(1turn)}}
</style>