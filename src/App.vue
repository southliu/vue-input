<script setup lang="ts">
import { ref } from 'vue';
import Input from './components/Input.vue';

const value = ref('');
const textareaValue = ref('');
</script>

<template>
  <div>
    <Input
      v-model="value"
      :clearable="true"
      placeholder="请输入"
    />
  </div>

  <div style="width: 400px; margin-top: 20px;">
    <Input
      v-model="textareaValue"
      :autosize="{ minRows: 2 }"
      type="textarea"
      placeholder="请输入内容"
    />
  </div>

  <div style="margin-top: 10px;">
    value: {{ value }}
  </div>

  <div style="margin-top: 10px;">
    textareaValue: {{ textareaValue }}
  </div>
</template>
