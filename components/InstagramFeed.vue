<template>
  <SfSection
    :title-heading="$t('Share your look')"
    subtitle-heading="#YOURLOOK"
    class="title"
  >
    <div class="grid grid-images">
      <div class="grid__row">
        <div class="grid__col">
          <nuxt-img
            class="smartphone-only"
            :src="'/homepage/imageAm.webp' | addBasePathFilter"
            alt="katherina_trn"
            :width="160"
            :height="160"
            provider="static"
          >
            katherina_trn
          </nuxt-img>
          <nuxt-img
            class="desktop-only"
            :src="'/homepage/imageAd.webp' | addBasePathFilter"
            alt="katherina_trn"
            :width="470"
            :height="470"
            provider="static"
          >
            katherina_trn
          </nuxt-img>
        </div>
        <div class="grid__col small">
          <nuxt-img
            class="smartphone-only"
            :src="'/homepage/imageBm.webp' | addBasePathFilter"
            alt="katherina_trn"
            :width="160"
            :height="160"
            provider="static"
          >
            katherina_trn
          </nuxt-img>
          <nuxt-img
            class="desktop-only"
            :src="'/homepage/imageCd.webp' | addBasePathFilter"
            alt="katherina_trn"
            :width="470"
            :height="160"
            provider="static"
          >
            katherina_trn
          </nuxt-img>
        </div>
      </div>
      <div class="grid__row">
        <div class="grid__col small">
          <nuxt-img
            class="smartphone-only"
            :src="'/homepage/imageCm.webp' | addBasePathFilter"
            alt="katherina_trn"
            :width="160"
            :height="160"
            provider="static"
          >
            katherina_trn
          </nuxt-img>
          <nuxt-img
            class="desktop-only"
            :src="'/homepage/imageBd.webp' | addBasePathFilter"
            alt="katherina_trn"
            :width="470"
            :height="160"
            provider="static"
          >
            katherina_trn
          </nuxt-img>
        </div>
        <div class="grid__col">
          <nuxt-img
            class="smartphone-only"
            :src="'/homepage/imageDm.webp' | addBasePathFilter"
            alt="katherina_trn"
            :width="160"
            :height="160"
            provider="static"
          >
            katherina_trn
          </nuxt-img>
          <nuxt-img
            class="desktop-only"
            :src="'/homepage/imageDd.webp' | addBasePathFilter"
            alt="katherina_trn"
            :width="470"
            :height="470"
            provider="static"
          >
            katherina_trn
          </nuxt-img>
        </div>
      </div>
    </div>
  </SfSection>
</template>
<script>
import { SfSection } from '@storefront-ui/vue';
export default {
  name: 'InstagramFeed',
  components: {
    SfSection
  }
};
</script>
<style lang="scss" scoped>
.title {
  --heading-title-font-weight: var(--font-weight--semibold);
  --section-margin: var(--spacer-xl) 0;
  --section-content-margin: var(--spacer-xl) 0;
  --heading-title-font-size: var(--h2-font-size);
  @include for-desktop {
    --section-margin: var(--spacer-2xl) 0;
    --section-content-margin: var(--spacer-2xl) 0;
  }
}
.grid {
  display: flex;
  max-height: 20.625rem;
  width: 100%;
  justify-content: center;
  margin: 0;
  @include for-desktop {
    max-height: 40.625rem;
    max-width: 60rem;
    margin: 0 auto;
  }
  &__row {
    display: flex;
    flex-direction: column;
    & + & {
      margin-left: var(--spacer-xs);
      @include for-desktop {
        margin-left: var(--spacer-sm);
      }
    }
  }
  &__col {
    width: 10rem;
    height: 10rem;
    @include for-desktop {
      &.small {
        height: 10rem;
      }
     width: 29.375rem;
     height: 29.375rem;
    }
    & + & {
      margin-top: var(--spacer-xs);
      @include for-desktop {
        margin-top: var(--spacer-sm);
      }
    }
  }
}
</style>
