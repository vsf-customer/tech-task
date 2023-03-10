<template>
  <div id="my-account">
    <SfBreadcrumbs
      class="breadcrumbs desktop-only"
      :breadcrumbs="breadcrumbs"
    />
    <SfContentPages
      v-e2e="'my-account-content-pages'"
      :title="$t('My Account')"
      :active="activePage"
      class="my-account"
      @click:change="changeActivePage"
    >
      <SfContentCategory :title="$t('Personal Details')">
        <SfContentPage :title="$t('My profile')">
          <MyProfile />
        </SfContentPage>

        <SfContentPage :title="$t('Shipping details')">
          <ShippingDetails />
        </SfContentPage>

        <SfContentPage :title="$t('Billing details')">
          <BillingDetails />
        </SfContentPage>

        <SfContentPage :title="$t('Loyalty card')">
          <LoyaltyCard />
        </SfContentPage>

        <SfContentPage :title="$t('My newsletter')">
          <MyNewsletter />
        </SfContentPage>
      </SfContentCategory>

      <SfContentCategory :title="$t('Order details')">
        <SfContentPage :title="$t('Order history')">
          <OrderHistory />
        </SfContentPage>

        <SfContentPage :title="$t('My reviews')">
          <MyReviews />
        </SfContentPage>
      </SfContentCategory>

      <SfContentPage :title="$t('Log out')" />
    </SfContentPages>
  </div>
</template>
<script>
import { SfBreadcrumbs, SfContentPages } from '@storefront-ui/vue';
import { computed, useContext, useRouter, useRoute } from '@nuxtjs/composition-api';
import { useUser } from '@vsf-enterprise/commercetools';
import { useUiNotification } from '~/composables';
import MyProfile from './MyAccount/MyProfile';
import ShippingDetails from './MyAccount/ShippingDetails';
import BillingDetails from './MyAccount/BillingDetails';
import LoyaltyCard from './MyAccount/LoyaltyCard';
import MyNewsletter from './MyAccount/MyNewsletter';
import OrderHistory from './MyAccount/OrderHistory';
import MyReviews from './MyAccount/MyReviews';

export default {
  name: 'MyAccount',
  components: {
    SfBreadcrumbs,
    SfContentPages,
    MyProfile,
    ShippingDetails,
    BillingDetails,
    LoyaltyCard,
    MyNewsletter,
    OrderHistory,
    MyReviews
  },
  middleware: [
    'is-authenticated'
  ],
  setup() {
    const { app: { i18n, localePath } } = useContext();
    const router = useRouter();
    const route = useRoute();
    const { logout, error: userError } = useUser();
    const { send } = useUiNotification();
    const activePage = computed(() => {
      const { pageName } = route.value.params;

      if (pageName) {
        return (pageName.charAt(0).toUpperCase() + pageName.slice(1)).replace('-', ' ');
      }
    });

    const changeActivePage = async (title) => {
      if (title === i18n.t('Log out')) {
        await logout();

        if (!userError.value.logout) {
          send({
            type: 'success',
            message: i18n.t('Successfully logged out')
          });
          router.push(localePath({ name: 'home' }));
          return;
        }
      }

      const slugifiedTitle = (title || '').toLowerCase().replace(' ', '-');
      const transformedPath = `/my-account/${slugifiedTitle}`;
      const localeTransformedPath = localePath(transformedPath);

      router.push(localeTransformedPath);
    };

    return { changeActivePage, activePage };
  },

  data() {
    return {
      breadcrumbs: [
        {
          text: 'Home',
          link: '#'
        },
        {
          text: 'My Account',
          link: '#'
        }
      ]
    };
  }
};
</script>

<style lang='scss' scoped>
#my-account {
  box-sizing: border-box;
  @include for-desktop {
    max-width: 1240px;
    margin: 0 auto;
  }
}
.my-account {
  @include for-mobile {
    --content-pages-sidebar-category-title-font-weight: var(
      --font-weight--normal
    );
    --content-pages-sidebar-category-title-margin: var(--spacer-sm)
      var(--spacer-sm) var(--spacer-sm) var(--spacer-base);
  }
  @include for-desktop {
    --content-pages-sidebar-category-title-margin: var(--spacer-xl) 0 0 0;
  }
}
.breadcrumbs {
  margin: var(--spacer-base) 0 var(--spacer-lg);
}
</style>
