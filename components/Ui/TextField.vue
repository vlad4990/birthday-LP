<script setup lang="ts">
import { watchDebounced } from '@vueuse/core'
const model = defineModel({
  set(newValue) {
    if(props.rules && props.rules.length){
      let result = newValue as string
      for(const rule of props.rules){
        result = rule(result)
      }
      return result?.toString()
    }
    return newValue?.toString()
  },
  get(value) {
    return value?.toString()
  }
})

const props = withDefaults(
    defineProps<{
      errorMessage?: string | null;
      description?: string | null;
      label?: string | null;
      rules?: ((value: string) => string)[] | null
    }>(),
    {
      errorMessage: null,
      description: null,
      label: null,
      rules: null
    }
);

const emit = defineEmits(['lazy-input']);
watchDebounced(
    model,
    () => { emit('lazy-input', model.value)},
    { debounce: 500, maxWait: 5000 },
)
</script>

<template>
  <div class="ui-text-field" :class="{'ui-text-field--filled': model?.length}" v-auto-animate>
    <div class="ui-text-field__container" :class="{'!outline-[#AA1515]': errorMessage}">
      <slot class="hello" name="prepend-inner"></slot>
      <div class="relative w-full">
        <div class="ui-text-field__label" :class="{'text-[#AA1515] !opacity-100': errorMessage}" v-if="label">{{ label }}</div>
        <input class="ui-text-field__input" type="text" v-model="model" :class="{'pt-4': label}">
      </div>
      <slot name="append-inner"></slot>
    </div>
    <div class="ml-2 text-sm font-medium text-white/50 mt-0.5" v-if="description && !errorMessage">
      {{ description }}
    </div>
    <div class="ml-2 text-xs mt-0.5 text-[#AA1515]" v-if="errorMessage">
      {{ errorMessage }}
    </div>
  </div>
</template>

<style scoped lang="scss">
.ui-text-field {
  &__container{
    font-family: Furore,serif;
    outline-offset: -1px;
    outline: rgba(0,0,0,0.1) solid 1px;
    border-radius: 8px;
    overflow: hidden;
    display: flex;
    gap: 8px;
    align-items: center;
    padding-left: 16px;
    padding-right: 16px;
    font-weight: 500;
  }

  &__label {
    position: absolute;
    pointer-events: none;
    top: 16px;
    left: 0;
    right: 0;
    font-size: 16px;
    transition: all ease-in-out .2s;
    white-space: nowrap;
  }

  &__input{
    height: 56px;
    background: transparent;
    color: black;
    outline: none;
    appearance: none;
    border-radius: 8px;
    width: calc(100% + 32px);
    padding-left: 16px;
    font-weight: 700;
    padding-right: 16px;
    margin-right: -16px;
    margin-left: -16px;
  }

  &:focus-within, &--filled {
    .ui-text-field__label {
      transform: translateY(-12px);
      font-size: 12px;
      opacity: .5;
    }
  }
}
</style>
