<template>
  <div
    class="s-input"
    @mouseenter="isFocus = true"
    @mouseleave="isFocus = false"
    :class="styleClass"
  >
    <input
      class="s-input__inner"
      :value="props.modelValue"
      @input="handleChangeInput"
      :disabled="props.disabled"
      v-bind="attrs"
    />

    <div
      v-if="props.clearable && isClearable"
      v-show="isFocus"
      class="s-input__suffix"
      @click="handleClear"
    >
      <CloseIcon class="close-icon" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, useAttrs } from 'vue';
import CloseIcon from './CloseIcon.vue'

interface InputProps {
  modelValue?: string | number;
  disabled?: boolean;
  size?: string;
  clearable?: boolean;
};

//组件发送事件类型
type InputEmits = {
  (e: "update:modelValue", value: string): void;
};

const attrs = useAttrs();

const emit = defineEmits<InputEmits>();

const isFocus = ref(true);
const isClearable = ref(false);

const props = withDefaults(defineProps<InputProps>(), {
  modelValue: '',
  size: 'default'
});

const styleClass = computed(() => {
  return {
    "is-disabled": props.disabled,
    [`k-input--${props.size}`]: props.size,
  };
});


// 处理输入框内容变化
const handleChangeInput = (event: Event) => {
  //可清除clearable
  (event.target as HTMLInputElement).value
    ? (isClearable.value = true)
    : (isClearable.value = false);

  emit("update:modelValue", (event.target as HTMLInputElement).value);
};

// 处理清除操作
const handleClear = () => {
  emit("update:modelValue", '');
};
</script>

<style scoped>
.s-input {
  font-size: 14px;
  display: inline-block;
  position: relative;

  .s-input__inner {
    background-color: #fff;
    border-radius: 4px;
    border: 1px solid #dcdfe6;
    box-sizing: border-box;
    color: #606266;
    display: inline-block;
    font-size: inherit;
    height: 40px;
    line-height: 40px;
    outline: none;
    padding: 0 15px;
    width: 100%;
    &::placeholder {
      color: #c2c2ca;
    }

    &:hover {
      border: 1px solid #c0c4cc;
    }

    &:focus {
      border: 1px solid #409eff;
    }
  }

  
  .s-input__suffix,
  .s-input__prefix {
      position: absolute;
      right: 10px;
      height: 100%;
      top: 0;
      display: flex;
      align-items: center;
      cursor: pointer;
      color: #c0c4cc;
      font-size: 15px;
  }

  .close-icon {
    width: 20px;
    height: 20px;
  }
}

.s-input.is-disabled {
  .s-input__inner {
    background-color: #f5f7fa;
    border-color: #e4e7ed;
    color: #c0c4cc;
    cursor: not-allowed;
    &::placeholder {
      color: #c3c4cc;
    }
  }
}

.s-input.s-input--large {
  .s-input__inner {
    height: 36px;
    &::placeholder {
      font-size: 15px;
    }
  }
}

.s-input.s-input--default {
  .s-input__inner {
    height: 32px;

    &::placeholder {
      font-size: 14px;
    }
  }
}

.s-input.s-input--small {
  .s-input__inner {
    height: 28px;

    &::placeholder {
      font-size: 13px;
    }
  }
}
</style>
