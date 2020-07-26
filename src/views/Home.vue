<template>
  <div class="home">
    <local-player></local-player>
  </div>
</template>

<script>

import LocalPlayer from '@/components/LocalPlayer.vue';

export default {
  name: 'home',
  components: {
    LocalPlayer,
  },
};
</script>
