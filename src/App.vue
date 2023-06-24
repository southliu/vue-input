<script setup lang="ts">
import { ref } from 'vue';
import Input from './components/Input.vue';

const value = ref('');
</script>

<template>
  <Input
    v-model="value"
    type="password"
    :clearable="true"
    placeholder="请输入"
  />

  <div style="margin-top: 10px;">
    value: {{ value }}
  </div>
</template>
