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

                <v-btn
                  :disabled="!keyword"
                  color="success"
                  loading="loading"
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
                  <v-list-tile-avatar>
                    {{ dateToString(event.started_at) }}
                  </v-list-tile-avatar>
                  <v-list-tile-content>
                    <v-list-tile-title v-html="event.title"></v-list-tile-title>
                  </v-list-tile-content>
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
    events: [],
    loading: false,
  }),
  methods: {
    dateToString(date) {
      return moment(date).format('MM/DD(ddd)')
    },
    search() {
      this.loading = true
      axios.get('https://connpass.com/api/v1/event/', {
        params: {
          keyword: this.keyword,
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
