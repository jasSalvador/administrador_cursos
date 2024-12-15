<template>
  <div>
    <NavbarComponent/>
  </div>
</template>

<script>
import NavbarComponent from "@/components/NavbarComponent.vue"
import Vue from "vue"
export default Vue.extend({
  name: 'App',
  components: {
    NavbarComponent
  },
  data: () => ({
    //
  }),
});
</script>
