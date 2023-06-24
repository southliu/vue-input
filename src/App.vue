<script setup lang="ts">
import { ref } from 'vue';
import Input from './components/Input.vue';

const value = ref('');
</script>

<template>
  <Input
    v-model="value"
    placeholder="请输入"
  />
  <div>
    value: {{ value }}
  </div>
</template>

<style scoped>

</style>
