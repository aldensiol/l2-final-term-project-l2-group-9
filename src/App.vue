<template>
  <div id="app">
    <Layout />
  </div>
</template>

<script>
import Layout from "./components/Layout.vue";

export default {
  name: "App",
  components: {
    Layout,
  },
  created() {
    this.$store.dispatch("checkUser");
  },
};
</script>
