<template>
  <div v-if="loading || loadingOca" class="grid">
    <div class="col-12 md:col-6 xl:col-3">
      <SkeletonCard />
    </div>
  </div>

  <div v-else-if="credentials && credentials.length" class="grid">
    <div
      v-for="(cred, index) in credentials"
      :key="index"
      class="col-12 md:col-6 xl:col-4"
    >
      <OcaCard :credential="cred" />
    </div>
  </div>

  <span v-else> {{ $t('credentials.wallet.notFound') }} </span>
</template>

<script setup lang="ts">
// Vue
import { onMounted } from 'vue';
// PrimeVue etc
import { useToast } from 'vue-toastification';
// State
import { useHolderStore } from '@/store';
import { storeToRefs } from 'pinia';
// Other components
import OcaCard from './credentialOcaCard/OcaCard.vue';
import SkeletonCard from '@/components/common/SkeletonCard.vue';

// The emits it can do (common things between table and card view handled in parent)
// defineEmits(['accept', 'delete', 'reject']);

const toast = useToast();

// State
const { loading, loadingOca, credentials } = storeToRefs(useHolderStore());
const holderStore = useHolderStore();

onMounted(async () => {
  holderStore.listCredentials();
  // Get the oca list avaliable, each card will fetch it's OCA though
  holderStore.listOcas().catch((err) => {
    console.error(err);
    toast.error(`Failed to load Credentials from your wallet: ${err}`);
  });
});
</script>
