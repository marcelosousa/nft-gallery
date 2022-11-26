<template>
  <b-button
    :type="type || 'is-primary'"
    :disabled="disabled"
    @click="clickHandler">
    <slot>
      {{ label }}
    </slot>
  </b-button>
</template>

<script lang="ts" setup>
import { defineAsyncComponent } from 'vue'
import { defineEmits } from '#app'

defineProps<{
  disabled?: boolean
  label?: string
  type?: string
}>()

const emit = defineEmits(['click'])

const clickHandler = (e) => {
  emit('click', e)
}
</script>
<style scoped>
.media-object {
  box-shadow: 4px 4px 0 0 rgba(0, 0, 0, 1);
  border: 1px solid black;
}
</style>
