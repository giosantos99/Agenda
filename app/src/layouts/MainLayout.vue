<template>
  <q-layout view="lHh Lpr lFf">
    <appHeader />

    <q-page-container class="container">
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import appHeader from 'components/Header/appHeader.vue'

export default {
  name: 'MainLayout',

  components: { appHeader }
}
</script>

<style lang="sass" scoped>
.container
  max-width: 2500px
  margin: 0 auto
</style>
