<template>
  <v-container
    fluid
    grid-list-lg
  >
    <v-layout
      row
      wrap
    >
      <v-flex
        xs12
        sm6
        offset-sm3
      >
        キーワード
        <v-card>
          <v-card-text>
            <v-text-field
              v-model="keyword"
              placeholder="Python 東京"
              required
            />
          </v-card-text>
        </v-card>
      </v-flex>
      <v-flex
        xs12
        sm6
        offset-sm3
      >
        期間
      </v-flex>
      <v-flex
        xs12
        sm4
        offset-sm2
      >
        <v-card>
          <v-card-text>
            <date-picker
              v-model="from"
              label="from"
            />
          </v-card-text>
        </v-card>
      </v-flex>
      <v-flex
        xs12
        sm4
      >
        <v-card>
          <v-card-text>
            <date-picker
              v-model="to"
              label="to"
              :min="from"
              :allowed-dates="allowedDates"
            />
          </v-card-text>
        </v-card>
      </v-flex>
      <v-flex
        xs12
        sm4
        offset-sm4
        text-xs-center
      >
        <v-btn
          color="success"
          :loading="loading"
          :disabled="loading"
          @click="search"
        >
          Search
        </v-btn>
      </v-flex>
    </v-layout>

    <v-layout
      v-show="events.length"
      text-xs-center
      row
      wrap
    >
      <v-flex>
        <v-card>
          <v-list two-line>
            <template v-for="(event, i) in events">
              <v-list-tile
                :key="event.event_id"
                @click="go(event.event_url)"
              >
                <v-list-tile-content>
                  <v-list-tile-title>{{ event.title }}</v-list-tile-title>
                  <v-list-tile-sub-title>{{ event.place }}</v-list-tile-sub-title>
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
              <v-divider
                v-if="i !== events.length - 1"
                :key="`divider-${event.event_id}`"
              />
            </template>
          </v-list>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import axios from 'axios'
import adapter from 'axios-jsonp'
import moment from 'moment'
import DatePicker from '@/components/DatePicker'

moment.locale('ja')

export default {
  components: {
    DatePicker,
  },
  data: () => ({
    keyword: '',
    from: moment().format('YYYY-MM-DD'),
    to: moment().add(1, 'week').format('YYYY-MM-DD'),
    events: [],
    loading: false,
  }),

  watch: {
    from(newFrom) {
      // Avoid from to reverse
      if (moment(newFrom).diff(this.to, 'days') > 0) {
        this.to = moment(this.from).add(1, 'week').format('YYYY-MM-DD')
      }
    },
  },
  methods: {
    dateToString(date) {
      return moment(date).format('MM/DD(ddd) HH:mm')
    },
    allowedDates(date) {
      return moment(date).diff(this.from, 'days') <= 30
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
    go(url) {
      window.open(url, '_blank')
    },
  },
}
</script>
