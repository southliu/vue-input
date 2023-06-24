<script setup lang="ts">
import { ref } from 'vue';
import Input from './components/Input.vue';
import CloseIcon from './components/CloseIcon.vue'
import EyeIcon from './components/EyeIcon.vue'

const value = ref('');
</script>

<template>
  <Input
    v-model="value"
    :clearable="false"
    placeholder="请输入"
  >
    <template #suffix>
      <CloseIcon style="width: 20px; height: 20px;" />
    </template>
    <template #prefix>
      <EyeIcon style="width: 20px; height: 20px;" />
    </template>
  </Input>

  <div style="margin-top: 10px;">
    value: {{ value }}
  </div>
</template>
