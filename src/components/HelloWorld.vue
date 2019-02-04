<template>
  <v-container>
    <v-layout
      text-xs-center
      wrap
    >
      <v-flex>
        <v-card>
          <v-card>
              <v-form
                ref="form"
              >
                <v-text-field
                  v-model="keyword"
                  :counter="10"
                  label="keyword"
                  required
                ></v-text-field>

                      <v-flex xs12 md4 offset-md2>
        <v-card>
          <v-menu
        :close-on-content-click="false"
        v-model="menu"
        :nudge-right="40"
        lazy
        transition="scale-transition"
        offset-y
        full-width
        min-width="290px"
      >
        <v-text-field
          slot="activator"
          v-model="from"
          label="Picker without buttons"
          prepend-icon="event"
          readonly
        ></v-text-field>
        <v-date-picker v-model="from" @input="menu = false"></v-date-picker>
      </v-menu>
        </v-card>
      </v-flex>
      <v-flex xs12 md4>
        <v-card>
          <v-card-title><h4>終了</h4></v-card-title>
          <v-card-text class="tc">
            <v-date-picker
              v-model="to"
              locale="ja-jp"
              :min="from"
              :allowed-dates="allowDates"
            />
          </v-card-text>
        </v-card>
</v-flex>

                <v-btn
                  color="success"
                  :loading="loading"
                  :disabled="loading"
                  @click="search"
                >
                  Search
                </v-btn>

                <v-btn
                  color="error"
                  @click="reset"
                >
                  Reset Form
                </v-btn>
              </v-form>
          </v-card>

          <v-card>
            <v-list two-line>
              <template v-for="event in events">
                <v-list-tile
                  :key="event.event_id"
                  @click="go(event.event_url)"
                >
                  <v-list-tile-content>
                    <v-list-tile-title v-html="event.title"></v-list-tile-title>
                    <v-list-tile-sub-title v-html="event.place"></v-list-tile-sub-title>
                  </v-list-tile-content>

                  <v-list-tile-action>
                    <v-list-tile-action-text>
                      {{ dateToString(event.started_at) }}
                      〜
                      {{ dateToString(event.ended_at) }}
                    </v-list-tile-action-text>
                    <v-list-tile-action-text>
                      {{ event.accepted + event.waiting }}/{{ event.limit }}
                    </v-list-tile-action-text>
                  </v-list-tile-action>
                </v-list-tile>
                <v-divider :key="`divider-${event.event_id}`"/>
              </template>
            </v-list>
          </v-card>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import axios from 'axios'
import adapter from 'axios-jsonp'
import moment from 'moment'

moment.locale('ja')

export default {
  data: () => ({
    keyword: '',
    from: moment().format('YYYY-MM-DD'),
    to: moment().add('week', 1).format('YYYY-MM-DD'),
    menu: false,
    events: [],
    loading: false,
  }),
  methods: {
    dateToString(date) {
      return moment(date).format('MM/DD(ddd) HH:mm')
    },
    allowDates(date) {
      return moment(date).date(1).diff(moment().date(1), 'months') === 0
    },
    search() {
      this.loading = true
      const diff = moment(this.to).diff(this.from, 'days')
      const from = moment(this.from).add(1, 'days')
      axios.get('https://connpass.com/api/v1/event/', {
        params: {
          keyword: this.keyword,
          ymd: [...Array(diff)].map(() => {
            from.add(1, 'days')
            return from.format('YYYYMMDD')
          }),
          order: 2,
        },
        adapter,
      })
        .then((response) => {
          this.loading = false
          this.events = response.data.events
        })
        .catch(() => { this.loading = false })
    },
    reset() {
      this.keyword = ''
    },
    go(url) {
      window.open(url, '_blank')
    },
  },
}
</script>

<style>

</style>
