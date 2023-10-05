<template>
  <div class="row border-radius q-mb-xs no-wrap justify-between items-center bg-primary q-pa-sm">
    <q-btn
      color="white"
      icon="arrow_left"
      flat
      dense
      round
      @click="$emit('changeMonth', 'prev')"
    >
    </q-btn>
    <div
      class="text-white"
      :class="!$q.screen.lt.md ? 'text-h6' : 'text-weight-bold'"
    >{{ date }}</div>
    <q-btn
      icon="arrow_right"
      color="white"
      flat
      dense
      round
      @click="$emit('changeMonth', 'next')"
    >
    </q-btn>
  </div>
</template>

<script>
export default {
  name: 'appCalendarioHeader',

  props: {
    date: { type: String }
  }
}
</script>

<style lang="sass" scoped>
.border-radius
  border-radius: 4px
</style>