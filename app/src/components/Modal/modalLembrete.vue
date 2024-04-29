<template>
  <q-card v-bind="$attrs" class="full-width">
    <q-card-section class="row justify-between items-center bg-primary">
      <span class="text-h6 text-white">{{ newEvent ? 'Adicionar Evento' : 'Editar Evento' }}</span>
      <q-btn
        icon="close"
        color="white"
        flat
        dense
        round
        @click="$emit('closeModal')"
      >
      </q-btn>
    </q-card-section>

    <q-form @submit="handleEvento">
      <q-card-section
        class="q-gutter-y-sm no-wrap scroll"
        style="max-height: 70vh"
      >
        <q-input
          v-model="form.title"
          label="Título"
          type="text"
          filled
          dense       
          lazy-rules
          :rules="rulesEmpty"           
        >
        </q-input>
        <q-input
          v-model="form.details"
          label="Descrição"
          type="textarea"
          filled
          dense                
        >
        </q-input>
        <q-input
          v-model="form.date"
          label="Data"
          type="date"
          filled
          dense
          lazy-rules
          :rules="rulesEmpty"                  
        >
        </q-input>
        <q-input
          v-model="form.time"
          label="Horário"
          type="time"
          filled
          dense
        >
        </q-input>
        <div class="column">
          <p>Selecione o nível de importância do evento</p>
          <q-radio
            v-for="item in levelsImpt"
            :key="item.model"
            v-model="form.important"
            :val="item.model"
            :label="item.label"
          />
        </div>
      </q-card-section>

      <q-card-actions class="row justify-end">
        <q-btn
          v-if="!newEvent"
          label="Excluir"
          color="primary"
          no-caps
          flat
          @click="$emit('excluirEvento', form)"
        >
        </q-btn>
        <q-btn
          :label="newEvent ? 'Adicionar' : 'Editar'"
          color="primary"
          no-caps
          type="submit"
        >
        </q-btn>
      </q-card-actions>
    </q-form>
  </q-card>
</template>

<script>
export default {
  name: 'appModalLembrete',

  props: {
    newEvent: { type: Boolean, default: true },
    campos: { type: Object }
  },

  data () {
    return {
      form: this.campos,
      levelsImpt: [
        { label: 'Baixo', model: 'baixo' },
        { label: 'Médio', model: 'medio' },
        { label: 'Alto', model: 'alto' },
      ],
      rulesEmpty: [ val => val && val.length > 0 || 'Por favor, preencha o campo acima!']
    }
  },

  methods: {
    handleEvento () {
      const action = this.newEvent ? 'addEvento' : 'editarEvento'

      this.$emit(action, this.form)
    }
  }
}
</script>

<style lang="sass" scoped>

</style>