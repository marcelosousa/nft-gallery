<template>
  <div class="buttons is-flex is-flex-direction-column">
    <b-tooltip
      v-for="action in actions"
      :key="action"
      append-to-body
      :active="disableButton(action)"
      position="is-left"
      :label="disabledToolTips[action]">
      <TheButton
        :label="actionLabel(action)"
        :type="iconType(action)"
        class="gallery-btn"
        :data-cy="action"
        @click="handleActionSelect(action)" />
    </b-tooltip>
  </div>
</template>

<script setup lang="ts">
import {
  ShoppingActionToolTips,
  ShoppingActions,
  getActionButtonColor,
  getActionButtonLabelKey,
} from '~/utils/shoppingActions'
import { TranslateResult } from 'vue-i18n/types'
import { TheButton } from '@kodadot1/brick'
import { defineEmits } from '#app'

const props = defineProps<{
  actions?: ShoppingActions[]
  isMakeOffersAllowed?: boolean
  price?: any
  disabledToolTips: ShoppingActionToolTips
}>()

const emit = defineEmits(['click'])
const { $i18n } = useNuxtApp()

const handleActionSelect = (action: ShoppingActions) => {
  emit('click', action)
  return action
}

const disableButton = (action: ShoppingActions): boolean => {
  return Object.keys(props.disabledToolTips).includes(action)
}

const iconType = (value: ShoppingActions): string => {
  return getActionButtonColor(value)
}

const actionLabel = (action: ShoppingActions): TranslateResult => {
  return $i18n.t(getActionButtonLabelKey(action, props.price))
}
</script>

<style scoped>
.gallery-btn {
  min-width: 160px;
}
</style>
