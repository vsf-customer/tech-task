<template>
  <div id="filters">
    <SfSidebar
      :visible="isFilterSidebarOpen"
      :title="$t('Filters')"
      :class="[
        'sidebar-filters',
        !areFiltersAvailable && 'sidebar-filters--empty'
      ]"
      @close="toggleFilterSidebar"
      :persistent="hasNotifications"
    >
      <template v-if="areFiltersAvailable">
        <div class="filters desktop-only">
          <div v-for="facet in facets" :key="facet.id">
            <SfHeading
              :level="4"
              :title="$t(facet.label)"
              class="filters__title sf-heading--left"
            />
            <div
              v-if="isFacetColor(facet)"
              class="filters__colors"
            >
              <SfColor
                v-for="option in facet.options"
                :key="`${facet.id}-${option.value}`"
                :color="option.value"
                :selected="isFilterSelected(facet, option)"
                class="filters__color"
                @click="() => selectFilter(facet, option)"
              />
            </div>
            <div v-else>
              <SfFilter
                v-for="option in facet.options"
                :key="`${facet.id}-${option.value}`"
                :label="option.id + `${option.count ? ` (${option.count})` : ''}`"
                :selected="isFilterSelected(facet, option)"
                class="filters__item"
                @change="() => selectFilter(facet, option)"
              />
            </div>
          </div>
        </div>
        <SfAccordion class="filters smartphone-only">
          <div v-for="facet in facets" :key="facet.id">
            <SfAccordionItem
              :header="facet.label"
              class="filters__accordion-item"
            >
              <SfFilter
                v-for="option in facet.options"
                :key="`${facet.id}-${option.id}`"
                :label="option.id"
                :selected="isFilterSelected(facet, option)"
                class="filters__item"
                @change="() => selectFilter(facet, option)"
              />
            </SfAccordionItem>
          </div>
        </SfAccordion>
      </template>
      <span
        v-else
        class="filters__empty-title"
      >
        {{ $t('No filters are available now.') }}
      </span>
      <template #content-bottom>
        <div class="filters__buttons">
          <SfButton
            class="sf-button--full-width"
            :aria-label="$t('Apply filters')"
            @click="applyFilters"
          >
            {{ $t('Done') }}
          </SfButton>
          <SfButton
            v-if="areFiltersAvailable"
            class="sf-button--full-width filters__button-clear"
            :aria-label="$t('Clear all filters')"
            @click="clearFilters"
          >
            {{ $t('Clear all') }}
          </SfButton>
        </div>
      </template>
    </SfSidebar>
  </div>
</template>

<script>
import {
  SfSidebar,
  SfButton,
  SfHeading,
  SfFilter,
  SfAccordion,
  SfColor
} from '@storefront-ui/vue';
import { ref, computed, watch, useContext } from '@nuxtjs/composition-api';
import { useFacet, facetGetters } from '@vsf-enterprise/commercetools';
import { useUiHelpers, useUiState, useUiNotification } from '~/composables';
import Vue from 'vue';

export default {
  name: 'FiltersSidebar',
  components: {
    SfButton,
    SfSidebar,
    SfFilter,
    SfAccordion,
    SfColor,
    SfHeading
  },
  setup() {
    const { app: { i18n } } = useContext();
    const { changeFilters, isFacetColor } = useUiHelpers();
    const { toggleFilterSidebar, isFilterSidebarOpen } = useUiState();
    const { send, notifications, hasNotifications } = useUiNotification();
    const { result, loading } = useFacet();

    const facets = computed(() => facetGetters.getGrouped(result.value, ['color', 'size']));
    const selectedFilters = ref({});
    const areFiltersAvailable = computed(() =>
      facets.value.length > 0 && facets.value.some((facet) => facet.options.length > 0)
    );

    const setSelectedFilters = (facets = []) => {
      selectedFilters.value = Array.isArray(facets) ? facets.reduce((prev, curr) => ({
        ...prev,
        [curr.id]: curr.options
          .filter(o => o.selected)
          .map(o => o.id)
      }), {}) : {};
    };

    const isFilterSelected = (facet, option) => (selectedFilters.value[facet.id] || []).includes(option.id);

    const selectFilter = (facet, option) => {
      if (!selectedFilters.value[facet.id]) {
        Vue.set(selectedFilters.value, facet.id, []);
      }

      if (selectedFilters.value[facet.id].find(f => f === option.id)) {
        selectedFilters.value[facet.id] = selectedFilters.value[facet.id].filter(f => f !== option.id);
        return;
      }

      selectedFilters.value[facet.id].push(option.id);
    };

    const clearFilters = () => {
      const isEmpty = Object.values(selectedFilters.value).every(filter => filter.length === 0);
      if (!isEmpty) {
        send({
          // TODO: Add MESSAGE_TYPES after move SFC to TS
          type: 'info',
          message: i18n.t('Do you want to clear filters?'),
          action: {
            text: i18n.t('Yes'),
            onClick: () => {
              selectedFilters.value = {};
              changeFilters(selectedFilters.value);
            }
          },
          persist: true
        });
      }
    };

    const applyFilters = () => {
      toggleFilterSidebar();
      if (areFiltersAvailable.value) {
        changeFilters(selectedFilters.value);
      }
    };

    watch(facets, setSelectedFilters, { immediate: true });

    return {
      loading,
      facets,
      isFacetColor,
      selectFilter,
      isFilterSelected,
      isFilterSidebarOpen,
      areFiltersAvailable,
      toggleFilterSidebar,
      selectedFilters,
      clearFilters,
      applyFilters,
      notifications,
      hasNotifications
    };
  }
};
</script>

<style lang="scss" scoped>
.sidebar-filters {
  --overlay-z-index: 3;
  --sidebar-title-display: none;
  --sidebar-top-padding: 0;
  @include for-desktop {
    --sidebar-content-padding: 0 var(--spacer-xl);
    --sidebar-bottom-padding: 0 var(--spacer-xl);
  }
  &--empty {
    ::v-deep .sf-sidebar__content {
      justify-content: center;
      align-items: center;
    }
  }
}
::v-deep .sf-sidebar__aside {
  --sidebar-z-index: 3;
}
.filters {
  &__title {
    --heading-title-font-size: var(--font-size--xl);
    margin: var(--spacer-xl) 0 var(--spacer-base) 0;
    &:first-child {
      margin: calc(var(--spacer-xl) + var(--spacer-base)) 0 var(--spacer-xs) 0;
    }
  }
  &__empty-title {
    color: var(--_c-dark-secondary);
    font: var(--font-weight--normal) var(--font-size--base)/1.6 var(--font-family--secondary);
  }
  &__colors {
    display: flex;
  }
  &__color {
    margin: var(--spacer-xs) var(--spacer-xs) var(--spacer-xs) 0;
  }
  &__chosen {
    color: var(--c-text-muted);
    font-weight: var(--font-weight--normal);
    font-family: var(--font-family--secondary);
    position: absolute;
    right: var(--spacer-xl);
  }
  &__item {
    --radio-container-padding: 0 var(--spacer-sm) 0 var(--spacer-xl);
    --radio-background: transparent;
    --filter-label-color: var(--c-secondary-variant);
    --filter-count-color: var(--c-secondary-variant);
    --checkbox-padding: 0 var(--spacer-sm) 0 var(--spacer-xl);
    padding: var(--spacer-sm) 0;
    border-bottom: 1px solid var(--c-light);
    &:last-child {
      border-bottom: 0;
    }
    @include for-desktop {
      --checkbox-padding: 0;
      margin: var(--spacer-sm) 0;
      border: 0;
      padding: 0;
    }
  }
  &__accordion-item {
    --accordion-item-content-padding: 0;
    position: relative;
    left: 50%;
    right: 50%;
    margin-left: -50vw;
    margin-right: -50vw;
    width: 100vw;
  }
  &__buttons {
    margin: var(--spacer-sm) 0;
  }
  &__button-clear {
    --button-background: var(--c-light);
    --button-color: var(--c-dark-variant);
    margin: var(--spacer-xs) 0 0 0;
  }
}
</style>
