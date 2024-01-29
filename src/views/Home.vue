<template>
  <HelloWorld />
</template>

<script setup>
import HelloWorld from "@/components/HelloWorld.vue";
(() => {
  fetch("http://localhost:5255/")
    .then((e) => e.json())
    .then((e) => console.log(e))
    .catch((e) => console.log(e));
})();
</script>
