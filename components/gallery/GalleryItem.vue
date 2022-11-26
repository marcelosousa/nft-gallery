<template>
  <section class="py-5 gallery-item">
    <div class="columns">
      <div class="column is-two-fifths">
        <MediaItem
          :key="nftImage"
          class="gallery-item-media"
          :src="nftImage"
          :animation-src="nftAnimation"
          :mime-type="nftMimeType"
          :title="nft?.name" />
      </div>
      <div class="column">
        <div class="is-flex is-justify-content-space-between">
          <div>
            <h1 class="title">{{ nft?.name }}</h1>
            <h2 class="subtitle">
              <nuxt-link
                :to="`/${urlPrefix}/collection/${nft?.collection.id}`"
                class="has-text-link">
                {{ nft?.collection.name }}
              </nuxt-link>
            </h2>
          </div>
          <div class="buttons is-align-content-start">
            <GalleryItemShareBtn />
            <GalleryItemMoreActionBtn class="ml-4" />
          </div>
        </div>

        <div class="is-flex is-flex-direction-row py-4">
          <IdentityItem
            v-if="nft?.issuer"
            label="Creator"
            :prefix="urlPrefix"
            :account="nft?.issuer" />
          <IdentityItem
            v-if="nft?.currentOwner !== nft?.issuer"
            label="Owner"
            :prefix="urlPrefix"
            :account="nft?.currentOwner || ''" />
        </div>
        <!-- LINE DIVIDER -->
        <hr />
        <div
          class="is-flex is-align-items-center is-justify-content-space-between">
          <div class="is-flex is-flex-direction-column">
            <div class="label mb-0">
              {{ $t('price') }}
            </div>
            <CommonTokenMoney
              :value="nft?.price"
              inline
              data-cy="money"
              class="heading heading-is-2" />
          </div>

          <div class="is-flex is-flex-direction-column">
            <template v-if="accountId">
              <!-- <TheButton :label="$t('buy')" class="gallery-btn" />
              <TheButton
                class="mt-5"
                :label="$t('nft.event.MAKE_OFFER')"
                type="is-secondary gallery-btn " /> -->
              <AvailableActionsBsx
                ref="actions"
                :account-id="accountId"
                :is-owner="isOwnerValue"
                :current-owner-id="nft?.currentOwner"
                :price="nft?.price"
                :nft-id="nft?.id"
                :delegate-id="nft?.delegate"
                :collection-id="nft?.collection.id"
                :frozen="nft?.isFrozen"
                :is-make-offers-allowed="true"
                :is-buy-allowed="isBuyAllowed"
                :ipfs-hashes="[nft?.image, nft?.animation_url, nft?.metadata]"
                @change="handleAction" />
            </template>
            <ConnectWalletButton v-else label="general.connect_wallet" />
          </div>
        </div>

        <!-- <p>{{ nft }}</p> -->
        <p>nftImage: {{ nftImage }}</p>
        <p>nftAnimation: {{ nftAnimation }}</p>
        <p>nftMimeType: {{ nftMimeType }}</p>
        <!-- <p>{{ nftMetadata }}</p> -->
      </div>
    </div>

    <div class="columns mt-6">
      <div class="column is-two-fifths">
        <GalleryItemDescription />
      </div>

      <div class="column">
        <GalleryItemActivity />
      </div>
    </div>

    <CarouselTypeRelated
      v-if="nft?.collection.id"
      class="mt-6"
      :collection-id="nft?.collection.id"
      data-cy="carousel-related" />

    <CarouselTypeVisited class="mt-6" />
  </section>
</template>

<script setup lang="ts">
import { IdentityItem, MediaItem, TheButton } from '@kodadot1/brick'

import { useGalleryItem } from './useGalleryItem'
import GalleryItemShareBtn from './GalleryItemShareBtn.vue'
import GalleryItemMoreActionBtn from './GalleryItemMoreActionBtn.vue'
import GalleryItemDescription from './GalleryItemDescription.vue'
import GalleryItemActivity from './GalleryItemActivity.vue'
import { isOwner } from '@/utils/account'

const { urlPrefix } = usePrefix()
const { nft, nftImage, nftAnimation, nftMimeType }: any = useGalleryItem()

const CarouselTypeRelated = defineAsyncComponent(
  () => import('@/components/carousel/CarouselTypeRelated.vue')
)
const CarouselTypeVisited = defineAsyncComponent(
  () => import('@/components/carousel/CarouselTypeVisited.vue')
)

const CommonTokenMoney = defineAsyncComponent(
  () => import('@/components/shared/CommonTokenMoney.vue')
)

const ConnectWalletButton = defineAsyncComponent(
  () => import('@/components/shared/ConnectWalletButton.vue')
)

const AvailableActionsBsx = defineAsyncComponent(
  () => import('@/components/bsx/Gallery/Item/AvailableActions.vue')
)

const { $store } = useNuxtApp()
const accountId = $store.getters['getAuthAddress']
const balance = $store.getters['getAuthBalance']

const isOwnerValue = isOwner(nft?.currentOwner, accountId)

const isBuyAllowed = computed(() => {
  if (!nft.price) {
    return false
  }
  return parseFloat(balance) > parseFloat(nft.price)
})

const offersUpdate = ({ offers }) => {
  // this.isMakeOffersAllowed = !offers.find(({ caller }) => {
  //   return caller === this.accountId
  // })
}
</script>

<style lang="scss" scoped>
.gallery-item {
  font-family: 'Work Sans';
}
</style>
