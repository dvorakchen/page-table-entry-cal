<script setup lang="ts">
import PageTableEntry from './components/page-table-entry.vue'
import VirtualAddress from './components/virtual-address.vue'

</script>

<template>
  <h1>RISC-V SV39</h1>
  <div>
    <h3>Virtual Address</h3>
    <VirtualAddress />
  </div>
  <hr>
  <div>
    <h3>Page Table Entry</h3>
    <PageTableEntry />
  </div>

</template>
