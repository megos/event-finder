<template>
  <v-container>
    <v-layout
      text-xs-center
      wrap
    >
      <v-flex>
        <v-card>
          <v-toolbar color="cyan" dark>
            <v-toolbar-side-icon></v-toolbar-side-icon>

            <v-toolbar-title>Inbox</v-toolbar-title>

            <v-spacer></v-spacer>

            <v-btn icon>
              <v-icon>search</v-icon>
            </v-btn>
          </v-toolbar>

          <v-list two-line>
            <template v-for="event in events">
              <v-list-tile
                :key="event.event_id"
              >
                <v-list-tile-content>
                  <v-list-tile-title v-html="event.title"></v-list-tile-title>
                </v-list-tile-content>
              </v-list-tile>
              <v-divider :key="`divider-${event.event_id}`"/>
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

  export default {
    data: () => ({
      events: [],
    }),
    mounted () {
      axios
        .get('https://connpass.com/api/v1/event/', {
          params: {
            keyword: 'vue.js'
          },
          adapter,
        })
        .then(response => this.events = response.data.events)
    },
  }
</script>

<style>

</style>
