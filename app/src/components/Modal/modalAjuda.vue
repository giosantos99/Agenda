<template>
  <q-card class="full-width">
    <q-card-section class="row justify-between items-center bg-primary">
      <span class="text-h6 text-white">Ajuda</span>
      <q-btn
        icon="close"
        color="white"
        flat
        dense
        round
        v-close-popup
      >
      </q-btn>
    </q-card-section>
    <q-card-section>
      <q-expansion-item
        v-for="info in informacoes"
        :key="info.gif"
        expand-separator
        :label="info.label"
        header-class="bg-pink text-white"
        expand-icon-class="text-white"
        header-style="border-radius: 6px;"
        class="q-mb-sm"
      >
        <q-card>
          <q-card-section class="flex flex-center">
            <p>{{ info.texto }}</p>
            <q-img
              :src="`${info.gif}.gif`"
              spinner-color="primary"
              class="full-width full-height"
            />
          </q-card-section>
        </q-card>
      </q-expansion-item>
    </q-card-section>
  </q-card>
</template>

<script>
export default {
  name: 'appModalAjuda',

  data () {
    return {
      informacoes: [
        {
          label: 'Adicionar',
          texto: 'Você poderá estar adicionando um novo evento clicando no botão do rodapé (ícone do calendário), ou se preferir clicando encima do dia no calendário. Segue abaixo o tutorial:',
          gif: 'add'
        },
        {
          label: 'Visualizar',
          texto: 'Você poderá estar visualizando todos os eventos do dia desejado clincando no calendário na área da data em que deseja. Segue abaixo o tutorial:',
          gif: 'show'
        },
        {
          label: 'Editar / Excluir',
          texto: 'Você poderá estar editando ou excluindo um evento clincando no calendário na área da data em que deseja realizar a ação e logo em seguida clicar no evento desejado. Segue abaixo o tutorial:',
          gif: 'edit'
        }
      ]
    }
  }
}
</script>

<style lang="sass" scoped>

</style>