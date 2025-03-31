<script setup lang="ts">
import { onClickOutside } from '@vueuse/core';

interface ISelectOption {
  label: string;
  value: string;
  disabled?: boolean;
}

const model = defineModel<ISelectOption | string | null>();

const props = withDefaults(
  defineProps<{
    errorMessage?: string | null;
    items: ISelectOption[] | string[] | null;
    rawValue?: boolean;
    placeholder?: string;
    label?: string | null;
    warn?: boolean;
    clearable?: boolean;
  }>(),
  {
    errorMessage: null,
    placeholder: 'Выберите значение',
    rawValue: false,
    items: null,
    label: null,
    warn: false,
    clearable: false,
  }
);

const prepItems = computed<ISelectOption[] | null>(() => {
  if (!props.items) return null;
  if (typeof props.items[0] === 'string') {
    return props.items.map((i) => ({ label: i, value: i }) as ISelectOption);
  }
  return props.items as ISelectOption[];
});

const warnState = computed<boolean>(() => props.warn);

const isOpened = ref(false);

const emit = defineEmits(['input', 'change']);

const selectHandler = (newVal: ISelectOption | string) => {
  model.value = newVal;
  isOpened.value = false;
  emit('input', newVal);
  emit('change', newVal);
};

const selectedItem = computed(() => {
  const placeholder = props.label ? null : props.placeholder;
  if (!props.items) return placeholder;
  if (typeof props.items[0] === 'string') {
    return model.value;
  }
  return (
    props.items.find(
      (i) => i.value === model.value || i.value === model.value?.value
    )?.label ?? placeholder
  );
});

const uiSelectRef = ref(null);

onClickOutside(uiSelectRef, () => (isOpened.value = false));
</script>

<template>
  <div class="ui-select relative" ref="uiSelectRef">
    <div
      @click="isOpened = !isOpened"
      class="ui-select__control"
      :class="{ '!outline-[#AA1515]': errorMessage || warnState }"
    >
      <slot name="selected" :value="selectedItem">
        <div @click.stop>
          <slot name="prepend-inner"></slot>
        </div>
        <div
          class="w-full h-full flex flex-col justify-center"
          :class="{ 'text-[#AA1515] !opacity-100': errorMessage }"
          v-auto-animate
        >
          <div v-if="label" :class="{ 'text-xs text-black/50': model }">
            <div v-html="label"></div>
          </div>
          <div v-if="selectedItem">
            <div v-html="selectedItem"></div>
          </div>
        </div>
      </slot>
      <div
        v-if="clearable && model"
        class="opacity-50 absolute right-12"
        @click.stop="selectHandler(null)"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="24"
          height="24"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
          class="icon icon-tabler icons-tabler-outline icon-tabler-x"
        >
          <path stroke="none" d="M0 0h24v24H0z" fill="none" />
          <path d="M18 6l-12 12" />
          <path d="M6 6l12 12" />
        </svg>
      </div>
      <div>
        <div @click.stop>
          <slot name="append-inner"></slot>
        </div>
        <svg
          class="transition-transform"
          :class="{ 'rotate-180': isOpened }"
          width="14"
          height="8"
          viewBox="0 0 14 8"
          fill="none"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            opacity="0.5"
            d="M1 1L7 7L13 1"
            stroke="black"
            stroke-width="1.5"
            stroke-linecap="round"
            stroke-linejoin="round"
          />
        </svg>
      </div>
    </div>
    <Transition name="slide-in-up">
      <div v-if="isOpened" class="ui-select__options">
        <ul v-if="prepItems">
          <li
            v-for="item in prepItems"
            :key="item.value"
            @click="selectHandler(rawValue ? item : item.value)"
            class="ui-select__option"
            :class="{
              'ui-select__option--selected': item.value === (model as string),
              'pointer-events-none opacity-50': item.disabled,
            }"
          >
            <slot name="option" :value="item">
              {{ item.label }}
            </slot>
          </li>
        </ul>
        <div v-else class="py-2 px-3" @click="isOpened = false">
          <slot name="empty-options"> Пусто :( </slot>
        </div>
      </div>
    </Transition>
    <div class="ml-2 text-xs mt-0.5 text-[#AA1515]" v-if="errorMessage">
      {{ errorMessage }}
    </div>
  </div>
</template>

<style scoped lang="scss">
.ui-select {
  width: 100%;
  font-family: Furore;
  &__control {
    display: flex;
    align-items: center;
    //gap: 8px;
    justify-content: space-between;
    padding: 0 16px;
    outline: rgba(0,0,0,0.1) solid 1px;
    min-height: 56px;
    border-radius: 8px;
    cursor: pointer;
    white-space: break-spaces;
  }

  &__options {
    position: absolute;
    top: calc(100% + 8px);
    left: 0;
    right: 0;
    height: fit-content;
    background: rgba(0, 0, 0, 0.05);
    backdrop-filter: blur(40px);
    border-radius: 8px;
    padding: 4px;
    z-index: 9999;
    outline: rgba(0,0,0,0.1) solid 1px;

    ul {
      flex-direction: column;
      display: flex;
      gap: 4px;
    }
    li{
      white-space: break-spaces;
    }
    li:not(:last-child){
      position: relative;
      &:after{
        position: absolute;
        left: 8px;
        right: 8px;
        top: calc(100% + 2px);
        height: 1px;
        background: rgba(0, 0, 0, 0.05);
        content: '';
      }
    }
  }

  &__option {
    padding: 4px 12px;
    border-radius: 8px;
    cursor: pointer;

    &--selected {
      background: rgba(0, 0, 0, 0.05);
    }

    &:hover {
      background: rgba(0, 0, 0, 0.1);
    }
  }
}
</style>
