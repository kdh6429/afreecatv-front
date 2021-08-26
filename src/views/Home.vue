<template>
  <div class="home">
    <data-distributor></data-distributor>
  </div>
</template>

<script>
import DataDistributor from "@/components/DataDistributor.vue";

export default {
  name: "home",
  components: {
    DataDistributor
  }
};
</script>