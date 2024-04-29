<template>
<q-page class="q-pa-sm">
  <template v-if="!loading">
    <section>
      <CalendarioHeader
        :date="calendario.date"
        @changeMonth="onChangeMonth"
      />
      
      <q-calendar-month
        v-if="calendario.month"
        v-model="calendario.selectedDate"
        ref="calendar"
        locale="pt-br"
        date-type="round"
        date-align="right"
        month-label-size="md"
        :class="!$q.screen.lt.md ? 'container' : ''"
        :hoverable="true"
        :focusable="true"
        :focus-type="['day']"
        :short-weekday-label="$q.screen.lt.md"
        :short-month-label="$q.screen.lt.md"
        :day-min-height="85"
        :day-height="85"
        animated
        bordered
        @moved="onMoved"
        @click-date="onClickDate"
        @click-day="onClickDay"
      >
        <template #day="{ scope: { timestamp } }">
          <template v-if="!$q.screen.lt.md">
            <div
              v-for="(event, index) in getEvents(timestamp.date)"
              :key="index"
            >
              <q-badge
                class="full-width text-white text-weight-medium"
                :class="`bg-${bgChips(event.important)} ${index < 2 ? 'cursor-pointer' : ''}`"
                @click.stop="index < 2 ? handleModal({ modal: true, newEvent: false, campos: event }) : ''"
              >
                <div class="title q-calendar__ellipsis">
                  {{ index < 2 ? event.title : `+${(getEvents(timestamp.date).length - 2)} evento(s)` }}
                </div>
                <q-tooltip
                  v-if="event.details && index < 2"
                >
                  {{ event.details }}
                </q-tooltip>
              </q-badge>
            </div>
          </template>
          <template v-else>
            <q-badge
              v-if="getEvents(timestamp.date).length"
              class="full-width text-white text-weight-medium bg-pink flex flex-center"
              style="margin-top: 40px;"
            >
              <div class="title q-calendar__ellipsis">
                {{ `+${getEvents(timestamp.date).length}` }}
              </div>
            </q-badge>
          </template>
        </template>
      </q-calendar-month>

      <div v-else class="row justify-center">
        <div class="column full-width container">
          <q-calendar-day
            v-model="calendario.selectedDate"
            ref="calendar"
            view="day"
            locale="pt-br"
            animated
          >
            <template #head-day-event="{ scope: { timestamp } }">
              <div
                v-if="getEventsSorted(timestamp.date).length"
                class="q-pa-sm no-wrap scroll q-pb-xl"
                style="max-height: calc(100vh - 200px);"
              >
                <q-list
                  bordered
                  class="rounded-borders"
                >
                  <q-item
                    v-for="event in getEventsSorted(timestamp.date)"
                    :key="event.id"
                    clickable
                    v-ripple
                    style="border: 1px solid #ccc"
                    @click="handleModal({ modal: true, newEvent: false, campos: event })"
                  >
                  <q-item-section
                    avatar
                    class="q-pr-md"
                    style="min-width: auto;"
                  >
                    <q-icon name="label_important" :color="bgChips(event.important)" />
                  </q-item-section>
                    <q-item-section>
                      <q-item-label class="text-weight-bold">{{ event.title }}</q-item-label>
                      <q-item-label
                        class="ellipsis text-weight-medium"
                        caption
                        style="max-width: calc(100vw - 170px)"
                      >
                        {{ event.details }}
                      </q-item-label>
                    </q-item-section>

                    <q-item-section
                      v-if="event.time"
                      side
                      top
                    >
                      <q-icon name="schedule" color="dark" />
                      <q-item-label
                        class="text-weight-medium"
                        caption
                      >
                        {{ event.time }}
                      </q-item-label>
                    </q-item-section>
                  </q-item>
                </q-list>
              </div>
              <div v-else class="flex flex-center q-pt-md">
                <p>Nenhum evento adicionado!</p>
              </div>
            </template>
          </q-calendar-day>
        </div>
      </div>

      <CalendarioFooter
        :month="calendario.month"
        @addEvento="handleModal({ modal: true, newEvent: true, campos: {} })"
        @voltarCalendario="calendario.month = true"
        @changeMonth="onChangeMonth"
      />

      <q-dialog v-model="form.modal" @blur="OnLimparForm">
        <modalLembrete
          v-bind="form"
          @addEvento="OnAddEvento"
          @editarEvento="OnEditarEvento"
          @excluirEvento="OnExcluirEvento"
          @closeModal="OnLimparForm"
        />
      </q-dialog>
    </section>
  </template>
  <template v-else>
    <q-skeleton square style="min-height: 80vh;" class="q-pt-md" />
  </template>
</q-page>
</template>

<script>
import modalLembrete from 'components/Modal/modalLembrete.vue'
import CalendarioHeader from 'components/Calendario/CalendarioHeader.vue'
import CalendarioFooter from 'components/Calendario/CalendarioFooter.vue'
import { QCalendarMonth, QCalendarDay, today } from '@quasar/quasar-ui-qcalendar/src/index.js'

export default {
  name: 'IndexPage',

  components: {
    modalLembrete,
    CalendarioHeader,
    CalendarioFooter,
    QCalendarMonth,
    QCalendarDay 
  },

  data () {
    return {
      loading: false,

      calendario: {
        date: '',
        month: true,
        selectedDate: today(),
      },

      form: {
        modal: false,
        newEvent: true,
        campos: {
          title: '',
          details: '',
          date: '',
          time: '',
          important: ''
        }
      },

      events: []
    }
  },

  methods: {
    async handleEventos () {
      try {
        this.loading = true

        const response = await this.$axios.get('http://localhost:3000/agenda')

        const { data } = await response

        this.events = Object.values(data)
      } catch (e) {
        this.$q.notify({
          color: 'negative',
          message: 'Ocorreu um erro ao tentar carregar os eventos!'
        })
      } finally {
        this.loading = false
      }
    },

    OnLimparForm () {
      this.handleModal({ modal: false, newEvent: false, campos: {} })
    },

    OnExcluirEvento ({ id }) {
      try {
        this.loading = true

        this.$q.dialog({
          title: 'Excluir',
          message: 'Tem certeza de que deseja excluir o evento ?',
          ok: { flat: false, color: 'primary' },
          cancel: { flat: true, color: 'primary' }
        }).onOk(async () => {
          await this.$axios.delete(`http://localhost:3000/agenda/${id}`)

          this.$q.notify({
            color: 'positive',
            message: 'Evento deletado com sucesso!'
          })

          this.handleEventos()
        })

        this.OnLimparForm()
      } catch (e) {
        this.$q.notify({
          color: 'negative',
          message: 'Ocorreu um erro ao tentar excluir o evento!'
        })
      } finally {
        this.loading = false
      }
    },

    hasExists (titulo, data) {
      const someFn = ({ title, date }) => title === titulo && date === data
      const result = this.events.some(someFn)

      return result
    },

    createId () {
      const getId = this.events.map(item => item.id.toString())

      const maxId = Math.max(...getId) + 1

      return this.events.length ? maxId.toString() : '1'
    },

    async OnAddEvento (evento) {
      const sameEvt = this.hasExists(evento.title, evento.date)

      if (sameEvt) {
        this.$q.notify({
          color: 'negative',
          message: 'Já existe um evento com este nome marcado para essa data!'
        })

        return
      }

      try {
        this.loading = true

        const id =  this.createId()

        await this.$axios.post('http://localhost:3000/agenda', { ...evento, id })

        this.OnLimparForm()

        this.$q.notify({
          color: 'positive',
          message: 'Evento adicionado com sucesso!'
        })

        this.handleEventos()
      } catch (e) {
        this.$q.notify({
          color: 'negative',
          message: 'Ocorreu um erro ao tentar adicionar o evento!'
        })
      } finally {
        this.loading = false
      }
    },

    async OnEditarEvento (evento) {
      const sameEvt = this.hasExists(evento.title, evento.date)

      if (sameEvt) {
        this.$q.notify({
          color: 'negative',
          message: 'Já existe um evento com este nome marcado para essa data!'
        })

        return
      }

      try {
        this.loading = true

        await this.$axios.patch(`http://localhost:3000/agenda/${evento.id}`, evento)

        this.OnLimparForm()

        this.$q.notify({
          color: 'positive',
          message: 'Evento editado com sucesso!'
        })

        this.handleEventos()
      } catch (e) {
        this.$q.notify({
          color: 'negative',
          message: 'Ocorreu um erro ao tentar editar o evento!'
        })
      } finally {
        this.loading = false
      }
    },

    handleModal ({ modal, newEvent, campos }) {
      this.form = { modal, newEvent, campos: JSON.parse(JSON.stringify(campos))}
    },

    onChangeMonth (action) {
      this.$refs.calendar[action]()

      if (action === 'moveToToday') this.calendario = { date: this.getDate, month: this.calendario.month }
    },

    getEvents (dt) {
      const filterFn = ({ date }) => date === dt

      return this.events.filter(filterFn)
    },

    getEventsSorted (dt) {
      const sortFn = (a, b) => a.time > b.time ? 1 : -1

      return this.getEvents(dt).sort(sortFn)
    },

    onMoved ({ date }) {
      const dataFormat = date.split('-')

      const [ano, mes] = [dataFormat[0], dataFormat[1]]

      const format = { month: 'long', year: 'numeric' }

      const dataEdit = Intl.DateTimeFormat('pt-br', format).format(new Date(ano, (mes - 1)))

      this.calendario.date = `${dataEdit.charAt(0).toUpperCase()}${dataEdit.slice(1)}`
    },

    randomDate (dt) {
      const options = { month: 'numeric', year: 'numeric' }
      const date = Intl.DateTimeFormat('pt-br', options).format(new Date())

      const dateFormat = date.split('/').reverse().join('-')

      return `${dateFormat}-${dt}`
    },

    onClickDate ({ scope }) {
      const { timestamp } = scope

      this.form = {
        modal: true,
        campos: {
          date: timestamp.date
        }
      }
    },

    onClickDay ({ scope }) {
      const { timestamp } = scope

      this.calendario = {
        month: false,
        selectedDate: timestamp.date,
        date: this.calendario.date
      }
    },
  },

  computed: {
    getDate () {
      const optins = { month: 'long', year: 'numeric' }

      const data = Intl.DateTimeFormat('pt-br', optins).format(new Date())

      return data.charAt(0).toUpperCase() + data.slice(1)
    },

    bgChips: {
      get () {
        return (valor) => {
          const strategy = {
            baixo: 'primary',
            medio: 'warning',
            alto: 'red'
          }

          return strategy[valor] || 'pink'
        }
      }
    }
  },

  mounted () {
    this.calendario.date = this.getDate

    this.handleEventos()
  }
}
</script>

<style lang="sass" scoped>
.container
  min-height: calc(100vh - 188px)
  max-height: calc(100vh - 188px)
</style>

<style lang="sass">
.q-calendar-day__body,
.q-calendar-day__head--intervals
  display: none!important

.q-calendar-day__head--day__event,
.q-calendar-day__head--day
  width: 100%!important
  max-width: 100%!important
</style>
