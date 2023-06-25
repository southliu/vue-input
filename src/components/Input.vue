<template>
  <div class="s-textarea" v-if="type === 'textarea'">
    <textarea
      class="s-textarea__inner"
      :style="textareaStyle"
      v-bind="attrs"
      ref="textarea"
      :value="modelValue"
      @input="handleChangeInput"
    />
  </div>

  <div
    v-else
    class="s-input"
    @mouseenter="isFocus = true"
    @mouseleave="isFocus = false"
    :class="styleClass"
  >
    <div class="s-input__prepend" v-if="slots.prepend">
      <slot name="prepend" />
    </div>

    <input
      ref="inputRef"
      class="s-input__inner"
      :class="{
        'icon-space': isClearable || isShowEye || isShowSuffixIcon,
        'pref-space': isShowPrefixIcon
      }"
      :value="modelValue"
      @input="handleChangeInput"
      :disabled="disabled"
      v-bind="attrs"
    />

    <div class="s-input__append" v-if="slots.append">
      <slot name="append" />
    </div>

    <div
      v-if="clearable && isClearable && !isShowEye"
      v-show="isFocus && modelValue !== ''  && modelValue !== null"
      class="s-input__suffix"
      @click="handleClear"
    >
      <CloseIcon class="icon" />
    </div>

    <div class="s-input__suffix" v-show="isShowEye">
      <EyeIcon
        class="icon"
        :type="eyeIcon"
        @click="handleChangeEye"
      />
    </div>

    <div class="s-input__prefix" v-if="isShowPrefixIcon">
      <slot name="prefix" />
    </div>
    <div class="s-input__suffix no-cursor" v-if="isShowSuffixIcon">
      <slot name="suffix" />
    </div>
  </div>
</template>

<script setup lang="ts">
import {
  ref,
  watch,
  computed,
  useSlots,
  useAttrs,
  shallowRef,
  nextTick
} from 'vue';
import { calcTextareaHeight, isObject } from '../utils/helper';
import CloseIcon from './CloseIcon.vue';
import EyeIcon from './EyeIcon.vue';

interface InputProps {
  modelValue?: string | number;
  disabled?: boolean;
  type?: string;
  size?: string;
  clearable?: boolean;
  autosize?: boolean | AutosizeObj;
};

type InputEmits = {
  (e: 'update:modelValue', value: string): void;
};

type AutosizeObj = {
    minRows?: number
    maxRows?: number
};

defineOptions({
  name: 'BasicInput'
});

const emit = defineEmits<InputEmits>();

const props = withDefaults(defineProps<InputProps>(), {
  modelValue: '',
  type: 'text',
  size: 'default'
});

const attrs = useAttrs();
const slots = useSlots();

const inputRef = ref();
const isFocus = ref(true);
const isClearable = ref(false);
const eyeIcon = ref('browse');
const textareaStyle = ref<any>();
const textarea = shallowRef<HTMLTextAreaElement>();

Promise.resolve().then(() => {
  if (props.type === 'password') {
    inputRef.value.type = 'password';
  }
});

const isShowEye = computed(() => {
  return (
    props.type === 'password' && props.modelValue
  );
});

const styleClass = computed(() => {
  return {
    'is-disabled': props.disabled,
    [`s-input--${props.size}`]: props.size,
    ["s-input-group s-input-prepend"]: slots.prepend,
    ["s-input-group s-input-append"]: slots.append,
  };
});

//带Icon输入框
const isShowSuffixIcon = computed(() => {
  return slots.suffix && !props.clearable && props.type !== 'password';
});
const isShowPrefixIcon = computed(() => {
  return slots.prefix;
});

watch(() => props.modelValue, () => {
    if (attrs.type === 'textarea' && props.autosize) {
        const minRows = isObject(props.autosize) ? (props.autosize as AutosizeObj).minRows : undefined;
        const maxRows = isObject(props.autosize) ? (props.autosize as AutosizeObj).maxRows : undefined;
        nextTick(() => {
            textareaStyle.value = calcTextareaHeight(textarea.value!, minRows, maxRows);
        });
    }
}, { immediate: true });

// 处理输入框内容变化
const handleChangeInput = (event: Event) => {
  //可清除clearable
  (event.target as HTMLInputElement).value
    ? (isClearable.value = true)
    : (isClearable.value = false);

  emit('update:modelValue', (event.target as HTMLInputElement).value);
};

// 处理清除操作
const handleClear = () => {
  emit('update:modelValue', '');
};

// 切换密码可视状态
const handleChangeEye = () => {
  if (inputRef.value.type === 'password') {
    eyeIcon.value = 'eye-close';
    inputRef.value.type = attrs.type || 'text';
    return;
  }

  inputRef.value.type = 'password';
  eyeIcon.value = 'browse';
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

  .s-input--prefix.s-input__inner {
    padding-left: 30px;
  }

  .s-input__prefix {
    position: absolute;
    width: 20px;
    cursor: default !important;
    left: 10px;
  }

  .pref-space {
    padding-left: 35px !important;
  }

  .icon-space {
    padding-right: 35px !important;
  }

  .icon {
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

.no-cursor {
  cursor: default !important;
}

.s-textarea {
  width: 100%;

  .s-textarea__inner {
    display: block;
    padding: 5px 15px;
    line-height: 1.5;
    box-sizing: border-box;
    width: 100%;
    font-size: inherit;
    color: #606266;
    background-color: #fff;
    background-image: none;
    border: 1px solid #dcdfe6;
    border-radius: 4px;

    &::placeholder {
      color: #c2c2ca;
    }

    &:hover {
      border: 1px solid #c0c4cc;
    }

    &:focus {
      outline: none;
      border: 1px solid #409eff;
    }
  }
}

.s-input.s-input-group.s-input-append,
.s-input.s-input-group.s-input-prepend {
  line-height: normal;
  display: inline-table;
  width: 100%;
  border-collapse: separate;
  border-spacing: 0;

  .s-input__inner {
    border-radius: 0 4px 4px 0;
  }

  .s-input__prepend,
  .s-input__append {
    background-color: #f5f7fa;
    color: #909399;
    vertical-align: middle;
    display: table-cell;
    position: relative;
    border: 1px solid #dcdfe6;
    border-radius: 4 0px 0px 4px;
    padding: 0 20px;
    width: 1px;
    white-space: nowrap;
  }

  .s-input__append {
    border-radius: 0 4px 4px 0px;
  }
}

.s-input.s-input-group.s-input-append {
  .s-input__inner {
    border-top-right-radius: 0px;
    border-bottom-right-radius: 0px;
  }
}
</style>
