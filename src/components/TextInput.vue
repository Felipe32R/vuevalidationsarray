<template>
  <div class="flex flex-col gap-1">
    <div class="flex flex-col gap-1">
      <div class="flex items-center gap-1 justify-between w-full">
        <label v-if="label?.length" class="text-sm text-gray-500" :for="id">
          {{ label }}
          <span v-if="required" class="text-sm text-red-500">*</span>
        </label>
        <span v-if="maxLength" class="text-[10px] opacity-50">
          {{ charactersLabel }}
        </span>
      </div>
      <div class="flex flex-col gap-1">
        <div
          class="flex items-stretch border border-gray-300 rounded-lg overflow-hidden"
          :class="{
            'border-primary': focused,
            'border-red-500': errors?.length,
            'opacity-50 bg-gray-300 cursor-not-allowed': disabled,
          }"
        >
          <input
            type="text"
            :value="modelValue ?? value ?? ''"
            :id="id"
            :placeholder="placeholder"
            :maxlength="maxLength"
            :disabled="disabled"
            @input.prevent="
              $emit(
                'update:modelValue',
                ($event.target as HTMLInputElement).value
              )
            "
            @focus="focused = true"
            @focusout="focused = false"
            class="outline-none w-full px-4 py-2 bg-transparent disabled:cursor-not-allowed"
          />
          <button
            type="button"
            @click="clearValue"
            class="text-gray-500 hover:text-gray-700 flex items-center justify-center px-4 hover:brightness-95 transition-all duration-200"
            v-if="showClear && modelValue && modelValue.length"
          >
            <i
              class="fi fi-ss-circle-xmark text-sm h-3 flex items-center justify-center"
            ></i>
          </button>
        </div>
      </div>
    </div>
    <div v-if="errors?.length" class="text-red-500 text-xs">
      {{ errors[0] }}
    </div>
  </div>
</template>

<script lang="ts" setup>
import { computed, ref } from "vue";

const emits = defineEmits<{
  (e: "update:modelValue", v: string): void;
}>();
const props = defineProps<{
  modelValue?: string | undefined;
  value?: string | undefined;
  showClear?: boolean;
  errors?: string[];
  label?: string;
  required?: boolean;
  id?: string;
  placeholder?: string;
  maxLength?: number;
  disabled?: boolean;
}>();

const focused = ref<boolean>(false);

const charactersLabel = computed(() => {
  if (props.maxLength) {
    return `${props.modelValue?.length ?? 0}/${props.maxLength}`;
  }
  return "";
});

const clearValue = () => {
  emits("update:modelValue", "");
};
</script>
