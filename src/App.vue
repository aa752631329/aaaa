<template>
  <div id="app">

    <mh-h-h-e-t-e-r v-if="this.$route.path=='/'"/>
    <mh-hea-ter v-else="this.$route.path=='/woshizhu'"/>
<router-view/>
    <mh-footer/>
  </div>
</template>

<script>
import MhHHETER from "@/components/mh-hHETER";
import MhFooter from "@/components/Mh-footer";
import MhHeaTer from "@/components/mh-heaTer";
export default {
  components: {MhHeaTer, MhFooter, MhHHETER}
}
</script>

<style scoped>

</style>
<style >
*{
  text-decoration: none;
  padding: 0;
  margin: 0;
  list-style: none;
}
</style>
