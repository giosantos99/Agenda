<template>
  <q-header
    elevated
    class="row justify-between items-center q-pa-sm bg-secondary"
  >
    <div class="row items-center q-gutter-x-sm">
      <div class="flex flex-center container">
        <q-img
          src="../../assets/logo.png"
          spinner-color="primary"
          contain
          class="img"
        >
        </q-img>
      </div>
      <span class="title">Agenda</span>
    </div>
    <q-btn
      icon="help"
      flat
      dense
      round
      @click="modal = true"
    >
      <q-tooltip>Ajuda</q-tooltip>
    </q-btn>

    <q-dialog v-model="modal">
      <modalAjuda />
    </q-dialog>
  </q-header>
</template>

<script>
import modalAjuda from 'components/Modal/modalAjuda.vue'

export default {
  name: 'appHeader',

  components: { modalAjuda },

  data () {
    return {
      modal: false
    }
  }
}
</script>

<style lang="sass" scoped>
.title
  font-weight: bold
  font-size: 1.65rem
.container
  min-width: 55px
  min-height: 55px
  background-color: red
  border-radius: 50%

  background: rgba( 255, 255, 255, 0.35 )
  box-shadow: 0 8px 32px 0 rgba( 31, 38, 135, 0.37 )
  backdrop-filter: blur( 13.5px )
  -webkit-backdrop-filter: blur( 13.5px )
  border: 1px solid rgba( 255, 255, 255, 0.18 )
  .img
    width: 100%
    max-width: 40px
</style>