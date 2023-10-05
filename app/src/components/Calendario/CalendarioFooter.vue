<template>
  <div class="row border-radius q-gutter-x-xs q-mt-xs justify-end bg-primary q-pa-xs">
    <q-btn
      v-if="!month"
      icon="arrow_back"
      color="white"
      flat
      dense
      round
      @click="$emit('voltarCalendario')"
    >
      <q-tooltip>Voltar para o calendário</q-tooltip>
    </q-btn>
    <q-btn
      icon="event"
      color="white"
      flat
      dense
      round
      @click="$emit('addEvento')"
    >
      <q-tooltip>Adicionar evento</q-tooltip>
    </q-btn>
    <q-btn
      icon="refresh"
      color="white"
      flat
      dense
      round
      @click="$emit('changeMonth', 'moveToToday')"
    >
      <q-tooltip>Voltar ao mês atual</q-tooltip>
    </q-btn>
  </div>
</template>

<script>
export default {
  name: 'appCalendarioFooter',

  props: {
    month: { type: Boolean }
  }
}
</script>

<style lang="sass" scoped>
.border-radius
  border-radius: 4px
</style>